import React, { useState, useEffect, useRef } from 'react';
import { 
  Shield, 
  User, 
  MessageSquare, 
  PlusSquare, 
  Heart, 
  Send, 
  LogOut, 
  Image as ImageIcon,
  Zap,
  Target,
  Flame
} from 'lucide-react';
import { initializeApp } from 'firebase/app';
import { 
  getAuth, 
  signInAnonymously, 
  signInWithCustomToken, 
  onAuthStateChanged 
} from 'firebase/auth';
import { 
  getFirestore, 
  collection, 
  doc, 
  setDoc, 
  getDoc, 
  addDoc, 
  onSnapshot, 
  serverTimestamp,
  query
} from 'firebase/firestore';

// --- FIREBASE CONFIG ---
// Note: These would typically come from your Firebase Console.
// Using placeholders for the environment to inject.
const firebaseConfig = JSON.parse(__firebase_config || '{}');
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const appId = typeof __app_id !== 'undefined' ? __app_id : 'marvel-social-app';

export default function App() {
  const [user, setUser] = useState(null);
  const [view, setView] = useState('feed'); // feed, chat, post, profile, auth
  const [posts, setPosts] = useState([]);
  const [chats, setChats] = useState([]);
  const [loading, setLoading] = useState(true);

  // Auth State Management
  useEffect(() => {
    const initAuth = async () => {
      try {
        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
          await signInWithCustomToken(auth, __initial_auth_token);
        } else {
          // If no custom token, we wait for the user to "sign in" via our custom UI
          // For this specific Marvel app, we simulate a local session until they "Login"
        }
      } catch (err) {
        console.error("Auth error", err);
      }
    };
    initAuth();

    const unsubscribe = onAuthStateChanged(auth, (u) => {
      setUser(u);
      setLoading(false);
      if (u) setView('feed');
      else setView('auth');
    });
    return () => unsubscribe();
  }, []);

  // Data Sync (Posts)
  useEffect(() => {
    if (!user) return;
    const postsRef = collection(db, 'artifacts', appId, 'public', 'data', 'posts');
    const unsubscribe = onSnapshot(postsRef, (snapshot) => {
      const p = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      setPosts(p.sort((a, b) => (b.createdAt?.seconds || 0) - (a.createdAt?.seconds || 0)));
    }, (err) => console.error("Firestore Error:", err));
    return () => unsubscribe();
  }, [user]);

  if (loading) return (
    <div className="h-screen bg-slate-950 flex items-center justify-center text-red-600">
      <Shield className="animate-spin w-12 h-12" />
    </div>
  );

  return (
    <div className="h-screen bg-slate-950 text-slate-100 flex flex-col font-sans overflow-hidden">
      {/* HUD Header */}
      <header className="p-4 border-b border-red-900/30 flex justify-between items-center bg-slate-900/50 backdrop-blur-md relative overflow-hidden">
        <div className="absolute top-0 left-0 w-full h-[1px] bg-gradient-to-r from-transparent via-red-500 to-transparent shadow-[0_0_10px_#ef4444]"></div>
        <div className="flex items-center gap-2">
          <Shield className="text-red-600 w-8 h-8 drop-shadow-[0_0_8px_rgba(220,38,38,0.5)]" />
          <h1 className="text-xl font-black tracking-tighter uppercase italic">Avengers Assembly</h1>
        </div>
        {user && (
          <button onClick={() => setView('profile')} className="hover:scale-110 transition-transform">
            <User className="w-6 h-6 text-red-400" />
          </button>
        )}
      </header>

      {/* Main Content Area */}
      <main className="flex-1 overflow-y-auto pb-20 scrollbar-hide">
        {view === 'auth' && <AuthView />}
        {view === 'feed' && <FeedView posts={posts} />}
        {view === 'post' && <CreatePost onComplete={() => setView('feed')} />}
        {view === 'chat' && <ChatView user={user} />}
        {view === 'profile' && <ProfileView user={user} />}
      </main>

      {/* Stark Tech Bottom Nav */}
      {user && (
        <nav className="fixed bottom-4 left-1/2 -translate-x-1/2 w-[90%] max-w-md bg-slate-900/80 backdrop-blur-xl border border-red-500/20 rounded-2xl p-4 flex justify-around items-center shadow-2xl">
          <button onClick={() => setView('feed')} className={`p-2 rounded-full ${view === 'feed' ? 'bg-red-600 text-white' : 'text-slate-400'}`}>
            <Zap size={24} />
          </button>
          <button onClick={() => setView('chat')} className={`p-2 rounded-full ${view === 'chat' ? 'bg-red-600 text-white' : 'text-slate-400'}`}>
            <MessageSquare size={24} />
          </button>
          <button onClick={() => setView('post')} className={`p-2 rounded-full ${view === 'post' ? 'bg-red-600 text-white' : 'text-slate-400'}`}>
            <PlusSquare size={24} />
          </button>
          <button onClick={() => setView('profile')} className={`p-2 rounded-full ${view === 'profile' ? 'bg-red-600 text-white' : 'text-slate-400'}`}>
            <Target size={24} />
          </button>
        </nav>
      )}
    </div>
  );
}

function AuthView() {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [birthday, setBirthday] = useState('');
  const [error, setError] = useState('');

  const handleLogin = async (e) => {
    e.preventDefault();
    if (!username || !password) return setError("Protocol incomplete. Fill all fields.");
    
    try {
      // In this specialized app, we use anonymous auth for the backend 
      // but store user metadata linked to their custom "username" in Firestore.
      await signInAnonymously(auth);
      const user = auth.currentUser;
      await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', user.uid), {
        username,
        birthday,
        powerLevel: Math.floor(Math.random() * 1000)
      });
    } catch (err) {
      setError("System failure: " + err.message);
    }
  };

  return (
    <div className="p-8 max-w-md mx-auto h-full flex flex-col justify-center">
      <div className="mb-8 text-center">
        <div className="w-20 h-20 bg-red-600 rounded-full mx-auto flex items-center justify-center mb-4 shadow-[0_0_30px_#ef4444]">
          <Shield size={40} className="text-white" />
        </div>
        <h2 className="text-3xl font-black uppercase italic">Identity Verification</h2>
        <p className="text-slate-500">Accessing S.H.I.E.L.D encrypted network...</p>
      </div>

      <form onSubmit={handleLogin} className="space-y-4">
        <div>
          <label className="text-xs font-bold text-red-500 uppercase tracking-widest">Codename</label>
          <input 
            type="text" 
            className="w-full bg-slate-800 border border-slate-700 rounded-lg p-3 focus:border-red-500 outline-none transition-all"
            placeholder="e.g. IronMan99"
            value={username}
            onChange={e => setUsername(e.target.value)}
          />
        </div>
        <div>
          <label className="text-xs font-bold text-red-500 uppercase tracking-widest">Encryption Key</label>
          <input 
            type="password" 
            className="w-full bg-slate-800 border border-slate-700 rounded-lg p-3 focus:border-red-500 outline-none transition-all"
            placeholder="Password"
            value={password}
            onChange={e => setPassword(e.target.value)}
          />
        </div>
        <div>
          <label className="text-xs font-bold text-red-500 uppercase tracking-widest">Activation Date</label>
          <input 
            type="date" 
            className="w-full bg-slate-800 border border-slate-700 rounded-lg p-3 focus:border-red-500 outline-none transition-all text-slate-400"
            value={birthday}
            onChange={e => setBirthday(e.target.value)}
          />
        </div>
        {error && <p className="text-red-500 text-xs font-bold">{error}</p>}
        <button className="w-full py-4 bg-red-600 rounded-xl font-black uppercase tracking-widest hover:bg-red-500 shadow-[0_0_20px_rgba(239,68,68,0.3)] transition-all">
          Initiate Protocol
        </button>
      </form>
    </div>
  );
}

function FeedView({ posts }) {
  return (
    <div className="flex flex-col gap-1">
      {posts.length === 0 ? (
        <div className="p-20 text-center text-slate-600">
          <Flame size={48} className="mx-auto mb-4 opacity-20" />
          <p className="font-bold italic uppercase">No intelligence gathered yet.</p>
        </div>
      ) : (
        posts.map(post => (
          <div key={post.id} className="relative aspect-[9/16] bg-slate-900 border-y border-slate-800 snap-start flex flex-col justify-end p-6">
            {post.imageUrl && (
              <img src={post.imageUrl} className="absolute inset-0 w-full h-full object-cover opacity-60" alt="Intel" />
            )}
            <div className="relative z-10 space-y-2 bg-gradient-to-t from-slate-950 via-slate-950/60 to-transparent p-4 -mx-6 -mb-6">
              <div className="flex items-center gap-2">
                <div className="w-10 h-10 bg-red-600 rounded-full flex items-center justify-center font-bold">
                  {post.author?.[0] || 'A'}
                </div>
                <div>
                  <p className="font-black italic text-red-500 uppercase">@{post.author || 'Avenger'}</p>
                  <p className="text-xs text-slate-400">Tactical Update</p>
                </div>
              </div>
              <p className="text-lg font-medium leading-tight">{post.content}</p>
              <div className="flex gap-6 pt-4">
                <button className="flex flex-col items-center gap-1 group">
                  <Heart className="group-hover:text-red-500 transition-colors" />
                  <span className="text-xs font-bold">4.2k</span>
                </button>
                <button className="flex flex-col items-center gap-1 group">
                  <MessageSquare className="group-hover:text-blue-400 transition-colors" />
                  <span className="text-xs font-bold">89</span>
                </button>
                <button className="flex flex-col items-center gap-1 group">
                  <Send className="group-hover:text-green-400 transition-colors" />
                  <span className="text-xs font-bold">Share</span>
                </button>
              </div>
            </div>
          </div>
        ))
      )}
    </div>
  );
}

function CreatePost({ onComplete }) {
  const [text, setText] = useState('');
  const [img, setImg] = useState('');
  const [isPosting, setIsPosting] = useState(false);

  const handlePost = async () => {
    if (!text) return;
    setIsPosting(true);
    try {
      const userProfile = await getDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', auth.currentUser.uid));
      const username = userProfile.exists() ? userProfile.data().username : 'AnonAvenger';

      await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'posts'), {
        content: text,
        imageUrl: img || `https://picsum.photos/seed/${Math.random()}/1080/1920`,
        author: username,
        createdAt: serverTimestamp()
      });
      onComplete();
    } catch (err) {
      console.error(err);
    } finally {
      setIsPosting(false);
    }
  };

  return (
    <div className="p-6 h-full flex flex-col gap-6">
      <h2 className="text-2xl font-black uppercase italic text-red-500">Log Tactical Update</h2>
      <textarea 
        className="flex-1 bg-slate-900 border border-slate-700 rounded-2xl p-4 outline-none focus:border-red-500 resize-none text-xl font-medium"
        placeholder="What's the status, Cap?"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <div className="flex gap-2">
        <input 
          type="text" 
          placeholder="Image URL (Optional)" 
          className="flex-1 bg-slate-800 border border-slate-700 p-3 rounded-lg text-sm"
          value={img}
          onChange={e => setImg(e.target.value)}
        />
        <button className="p-3 bg-slate-800 rounded-lg text-red-500"><ImageIcon /></button>
      </div>
      <button 
        disabled={isPosting}
        onClick={handlePost}
        className="w-full py-4 bg-red-600 rounded-xl font-black uppercase italic tracking-widest flex items-center justify-center gap-2 disabled:opacity-50"
      >
        {isPosting ? 'Broadcasting...' : <>Transmit <Send size={18} /></>}
      </button>
    </div>
  );
}

function ChatView({ user }) {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');

  useEffect(() => {
    const q = collection(db, 'artifacts', appId, 'public', 'data', 'messages');
    const unsubscribe = onSnapshot(q, (snapshot) => {
      const m = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      setMessages(m.sort((a, b) => (a.createdAt?.seconds || 0) - (b.createdAt?.seconds || 0)));
    });
    return () => unsubscribe();
  }, []);

  const sendMsg = async (e) => {
    e.preventDefault();
    if (!input.trim()) return;
    const userProfile = await getDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', user.uid));
    await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'messages'), {
      text: input,
      uid: user.uid,
      author: userProfile.data()?.username || 'Avenger',
      createdAt: serverTimestamp()
    });
    setInput('');
  };

  return (
    <div className="flex flex-col h-full bg-slate-950">
      <div className="flex-1 overflow-y-auto p-4 space-y-4">
        {messages.map(msg => (
          <div key={msg.id} className={`flex flex-col ${msg.uid === user.uid ? 'items-end' : 'items-start'}`}>
            <span className="text-[10px] uppercase font-black text-slate-500 mb-1 px-2">@{msg.author}</span>
            <div className={`max-w-[80%] p-3 rounded-2xl font-medium ${msg.uid === user.uid ? 'bg-red-600 text-white rounded-tr-none' : 'bg-slate-800 text-slate-200 rounded-tl-none'}`}>
              {msg.text}
            </div>
          </div>
        ))}
      </div>
      <form onSubmit={sendMsg} className="p-4 border-t border-slate-800 flex gap-2">
        <input 
          type="text" 
          className="flex-1 bg-slate-900 border border-slate-700 rounded-full px-4 py-2 outline-none focus:border-red-500"
          placeholder="Secure channel active..."
          value={input}
          onChange={e => setInput(e.target.value)}
        />
        <button className="bg-red-600 p-2 rounded-full text-white"><Send size={20} /></button>
      </form>
    </div>
  );
}

function ProfileView({ user }) {
  const [profile, setProfile] = useState(null);

  useEffect(() => {
    const fetch = async () => {
      const d = await getDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', user.uid));
      if (d.exists()) setProfile(d.data());
    };
    fetch();
  }, [user]);

  return (
    <div className="p-8 space-y-8">
      <div className="flex flex-col items-center text-center">
        <div className="w-24 h-24 bg-gradient-to-tr from-red-600 to-yellow-500 rounded-full p-1 mb-4">
          <div className="w-full h-full bg-slate-950 rounded-full flex items-center justify-center">
            <User size={48} className="text-red-500" />
          </div>
        </div>
        <h2 className="text-3xl font-black uppercase italic">@{profile?.username || 'Hero'}</h2>
        <div className="flex gap-4 mt-2">
          <div className="bg-slate-900 border border-red-900/50 px-3 py-1 rounded-full text-[10px] font-bold uppercase tracking-widest text-red-500">
            Power: {profile?.powerLevel || '---'}
          </div>
          <div className="bg-slate-900 border border-blue-900/50 px-3 py-1 rounded-full text-[10px] font-bold uppercase tracking-widest text-blue-500">
            Active Status
          </div>
        </div>
      </div>

      <div className="grid grid-cols-2 gap-4">
        <div className="bg-slate-900/50 border border-slate-800 p-4 rounded-2xl">
          <p className="text-[10px] font-bold text-slate-500 uppercase mb-1">Affiliation</p>
          <p className="font-black italic uppercase">Earth-616</p>
        </div>
        <div className="bg-slate-900/50 border border-slate-800 p-4 rounded-2xl">
          <p className="text-[10px] font-bold text-slate-500 uppercase mb-1">Birthday</p>
          <p className="font-black italic uppercase">{profile?.birthday || 'Classified'}</p>
        </div>
      </div>

      <button 
        onClick={() => auth.signOut()}
        className="w-full py-4 border border-red-600/30 text-red-500 rounded-xl font-black uppercase tracking-widest flex items-center justify-center gap-2 hover:bg-red-600/10 transition-colors"
      >
        <LogOut size={18} /> Abort Mission
      </button>
    </div>
  );
}

