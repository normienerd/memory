import React, { useState, useEffect, useRef } from 'react';
import { MessageCircle, Inbox, Send, Circle, CheckCircle2, Clock, FileText, Brain, Calendar as CalendarIcon, ChevronLeft, ChevronRight } from 'lucide-react';
import { initializeApp } from 'firebase/app';
import { getAuth, signInWithCustomToken, signInAnonymously, onAuthStateChanged } from 'firebase/auth';
import { getFirestore, collection, onSnapshot, setDoc, doc } from 'firebase/firestore';

const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';

export default function App() {
  const [activeTab, setActiveTab] = useState('chat'); // 'chat', 'vault', 'calendar'
  const [messages, setMessages] = useState([
    { role: 'ai', text: "Ami apnar memory assistant. Ki mone rakhte hobe bolen, ami guchiye rakhbo." }
  ]);
  const [memories, setMemories] = useState([]);
  const [isThinking, setIsThinking] = useState(false);
  const [user, setUser] = useState(null);

  useEffect(() => {
    const initAuth = async () => {
      try {
        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
          await signInWithCustomToken(auth, __initial_auth_token);
        } else {
          await signInAnonymously(auth);
        }
      } catch (error) {
        console.error("Firebase auth error:", error);
      }
    };
    initAuth();
    const unsubscribe = onAuthStateChanged(auth, setUser);
    return () => unsubscribe();
  }, []);

  useEffect(() => {
    if (!user) return;

    const memoriesRef = collection(db, 'artifacts', appId, 'users', user.uid, 'memories');
    const unsubscribe = onSnapshot(memoriesRef, (snapshot) => {
      const fetchedMemories = [];
      snapshot.forEach((document) => {
        fetchedMemories.push({ id: document.id, ...document.data() });
      });
      // Sort newest to oldest
      fetchedMemories.sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt));
      setMemories(fetchedMemories);
    }, (error) => {
      console.error("Firestore error:", error);
    });

    return () => unsubscribe();
  }, [user]);

  const handleSendMessage = async (text) => {
    setMessages((prev) => [...prev, { role: 'user', text }]);
    setIsThinking(true);

    try {
      const apiKey = ""; // The execution environment provides this key
      const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;

      const conversationHistory = messages.map(m => `${m.role === 'user' ? 'User' : 'Mone Rakhi'}: ${m.text}`).join('\n');
      const fullPrompt = `Here is the conversation so far:\n${conversationHistory}\n\nNow the user says: ${text}`;

      const payload = {
        contents: [{ parts: [{ text: fullPrompt }] }],
        systemInstruction: {
          parts: [{ text: `You are Mone Rakhi, a calm, minimalist AI memory assistant designed to reduce the user's mental load. 
Current Date/Time: ${new Date().toLocaleString()}. 
Analyze the user's latest input within the context of the conversation. 

RULES FOR EXTRACTION:
1. Extract new actionable items (reminders, tasks, notes) that the user wants you to remember.
2. DUE DATE REQUIREMENT: If the user asks you to remember a 'task' or 'reminder', check if they provided a due date/time. 
   - If NO due date is provided: DO NOT add the item to the 'items' array yet. Instead, use your 'ai_response' to politely ask them when it is due in casual Banglish.
   - If a due date IS provided (or if they say "no due date"), extract it into the 'items' array.
3. 'note' types do not require a due date and can be added immediately.
4. Respond directly with a brief, calming confirmation or question (under 2 sentences) in 'ai_response'.
5. Always output strictly valid JSON matching the schema.
6. LANGUAGE REQUIREMENT: You MUST communicate with the user entirely in casual, everyday "Banglish" (Bengali language written using English alphabets). Avoid overly formal words. For example: "Ami eita save kore rakhlam" or "Eita kobe mone koriye dibo?".
7. TASK REPHRASING: When saving the 'content' of a task or reminder, convert the user's first-person statement into an actionable task in Banglish. For example, if the user says "ami office e mail dibo", save the content as "Office e mail dite hobe" or "Office e mail dewa lagbe".` }]
        },
        generationConfig: {
          responseMimeType: "application/json",
          responseSchema: {
            type: "OBJECT",
            properties: {
              ai_response: { type: "STRING", description: "Your brief, calming conversational reply to the user." },
              items: {
                type: "ARRAY",
                items: {
                  type: "OBJECT",
                  properties: {
                    type: { type: "STRING", enum: ["reminder", "task", "note"] },
                    content: { type: "STRING", description: "The specific detail to remember." },
                    due_date: { type: "STRING", description: "Clear, readable date/time (e.g. 'Tomorrow at 5 PM', 'Monday') if mentioned, else null", nullable: true },
                    iso_date: { type: "STRING", description: "ISO 8601 formatted date (YYYY-MM-DD) corresponding to the due date, if applicable.", nullable: true }
                  },
                  required: ["type", "content"]
                }
              }
            },
            required: ["ai_response", "items"]
          }
        }
      };

      const fetchWithRetry = async (retries = 5) => {
        const delays = [1000, 2000, 4000, 8000, 16000];
        for (let i = 0; i < retries; i++) {
          try {
            const response = await fetch(url, {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify(payload)
            });
            if (!response.ok) throw new Error("API Error");
            return await response.json();
          } catch (err) {
            if (i === retries - 1) throw err;
            await new Promise(r => setTimeout(r, delays[i]));
          }
        }
      };

      const data = await fetchWithRetry();
      let responseText = data.candidates[0].content.parts[0].text;
      
      // Clean up potential markdown formatting from Gemini
      responseText = responseText.replace(/```json/g, '').replace(/```/g, '').trim();
      const parsed = JSON.parse(responseText);

      if (parsed.items && parsed.items.length > 0 && user) {
        for (const item of parsed.items) {
          const memoryId = crypto.randomUUID();
          const memoryData = {
            type: item.type,
            content: item.content,
            dueDate: item.due_date,
            isoDate: item.iso_date || null,
            completed: false,
            createdAt: new Date().toISOString()
          };
          await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'memories', memoryId), memoryData);
        }
      }

      setMessages((prev) => [...prev, { role: 'ai', text: parsed.ai_response }]);

    } catch (error) {
      console.error("Assistant Error:", error);
      // Fallback in case of API issues
      const isReminder = text.toLowerCase().includes('remind') || text.toLowerCase().includes('tomorrow');
      const memoryId = crypto.randomUUID();
      const newItem = {
        type: isReminder ? 'reminder' : 'note',
        content: text,
        dueDate: isReminder ? 'Upcoming' : null,
        isoDate: isReminder ? new Date().toISOString().split('T')[0] : null,
        completed: false,
        createdAt: new Date().toISOString()
      };
      
      if (user) {
        await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'memories', memoryId), newItem);
      }
      
      setMessages((prev) => [...prev, { role: 'ai', text: "Ami eita vault e save kore rakhlam." }]);
    } finally {
      setIsThinking(false);
    }
  };

  const toggleMemory = async (id) => {
    if (!user) return;
    const memory = memories.find(m => m.id === id);
    if (memory) {
      await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'memories', id), { completed: !memory.completed }, { merge: true });
    }
  };

  return (
    <div className="flex justify-center bg-slate-100 dark:bg-slate-950 min-h-screen font-sans selection:bg-teal-200 dark:selection:bg-teal-900 transition-colors duration-300">
      
      <div className="w-full max-w-md bg-white dark:bg-slate-900 shadow-2xl h-screen flex flex-col overflow-hidden sm:h-[90vh] sm:mt-[5vh] sm:rounded-[2.5rem] sm:border sm:border-slate-200 dark:sm:border-slate-800 relative transition-colors duration-300">
        
        {/* Header */}
        <header className="px-6 py-4 border-b border-slate-100 dark:border-slate-800 bg-white/90 dark:bg-slate-900/90 backdrop-blur-md z-10 flex-none flex items-center justify-between transition-colors duration-300">
          <div className="flex items-center space-x-3">
            <div className="w-9 h-9 bg-teal-600 rounded-full flex items-center justify-center shadow-sm">
              <Brain className="text-white" size={20} />
            </div>
            <h1 className="text-xl font-semibold text-slate-800 dark:text-slate-100 tracking-tight transition-colors duration-300">Mone Rakhi</h1>
          </div>
        </header>

        {/* Main Workspace */}
        <main className="flex-1 overflow-hidden bg-[#FAFAFA] dark:bg-[#0f172a] relative transition-colors duration-300">
          {activeTab === 'chat' && (
            <ChatView messages={messages} onSend={handleSendMessage} isThinking={isThinking} />
          )}
          {activeTab === 'vault' && (
            <VaultView memories={memories} toggleMemory={toggleMemory} />
          )}
          {activeTab === 'calendar' && (
            <CalendarView memories={memories} toggleMemory={toggleMemory} />
          )}
        </main>

        {/* Bottom Navigation */}
        <nav className="flex-none bg-white dark:bg-slate-900 border-t border-slate-100 dark:border-slate-800 flex justify-around py-2 pb-safe shadow-[0_-4px_20px_rgba(0,0,0,0.02)] z-10 transition-colors duration-300">
          <NavButton active={activeTab === 'chat'} icon={MessageCircle} label="Assistant" onClick={() => setActiveTab('chat')} />
          <NavButton active={activeTab === 'vault'} icon={Inbox} label="Vault" onClick={() => setActiveTab('vault')} />
          <NavButton active={activeTab === 'calendar'} icon={CalendarIcon} label="Calendar" onClick={() => setActiveTab('calendar')} />
        </nav>
      </div>
    </div>
  );
}

/* Subcomponents */

function NavButton({ active, icon: Icon, label, onClick }) {
  return (
    <button
      onClick={onClick}
      className={`flex flex-col items-center justify-center w-20 h-14 transition-colors duration-300 ${active ? 'text-teal-600 dark:text-teal-400' : 'text-slate-400 dark:text-slate-500 hover:text-slate-600 dark:hover:text-slate-300'}`}
    >
      <Icon size={22} className={active ? 'stroke-[2.5px]' : 'stroke-2'} />
      <span className="text-[11px] font-medium mt-1.5">{label}</span>
    </button>
  );
}

function ChatView({ messages, onSend, isThinking }) {
  const [input, setInput] = useState('');
  const bottomRef = useRef(null);

  useEffect(() => {
    bottomRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages, isThinking]);

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!input.trim() || isThinking) return;
    onSend(input.trim());
    setInput('');
  };

  return (
    <div className="flex flex-col h-full relative">
      <div className="flex-1 overflow-y-auto p-5 space-y-5">
        <div className="text-center mb-6 mt-4">
          <span className="text-xs font-medium text-slate-400 dark:text-slate-500 bg-slate-100 dark:bg-slate-800/50 px-3 py-1 rounded-full uppercase tracking-widest transition-colors duration-300">Today</span>
        </div>
        
        {messages.map((m, i) => (
          <div key={i} className={`flex ${m.role === 'user' ? 'justify-end' : 'justify-start'}`}>
            <div className={`max-w-[85%] p-3.5 text-[15px] leading-relaxed rounded-2xl transition-colors duration-300 ${
              m.role === 'user' 
                ? 'bg-teal-600 text-white rounded-br-sm shadow-sm' 
                : 'bg-white dark:bg-slate-800 text-slate-700 dark:text-slate-200 shadow-sm border border-slate-100 dark:border-slate-700 rounded-bl-sm'
            }`}>
              {m.text}
            </div>
          </div>
        ))}
        
        {isThinking && (
          <div className="flex justify-start">
            <div className="max-w-[80%] p-4 rounded-2xl bg-white dark:bg-slate-800 shadow-sm border border-slate-100 dark:border-slate-700 rounded-bl-sm flex space-x-1.5 items-center transition-colors duration-300">
              <div className="w-2 h-2 bg-slate-300 dark:bg-slate-500 rounded-full animate-bounce"></div>
              <div className="w-2 h-2 bg-slate-300 dark:bg-slate-500 rounded-full animate-bounce delay-75"></div>
              <div className="w-2 h-2 bg-slate-300 dark:bg-slate-500 rounded-full animate-bounce delay-150"></div>
            </div>
          </div>
        )}
        <div ref={bottomRef} className="h-2" />
      </div>

      <div className="flex-none p-4 bg-white dark:bg-slate-900 border-t border-slate-100 dark:border-slate-800 shadow-[0_-2px_15px_rgba(0,0,0,0.02)] transition-colors duration-300">
        <form onSubmit={handleSubmit} className="flex items-center space-x-3 bg-slate-100 dark:bg-slate-800 rounded-full p-1.5 pl-5 transition-all duration-300 focus-within:ring-2 focus-within:ring-teal-100 dark:focus-within:ring-teal-900/50 focus-within:bg-white dark:focus-within:bg-slate-900 border border-transparent focus-within:border-teal-200 dark:focus-within:border-teal-700/50">
          <input
            type="text"
            value={input}
            onChange={e => setInput(e.target.value)}
            placeholder="Tell me what to remember..."
            className="flex-1 bg-transparent outline-none text-slate-700 dark:text-slate-200 placeholder-slate-400 dark:placeholder-slate-500 text-[15px]"
          />
          <button 
            type="submit" 
            disabled={!input.trim() || isThinking} 
            className="p-2.5 bg-teal-600 text-white rounded-full disabled:bg-teal-300 dark:disabled:bg-teal-800 disabled:opacity-70 transition-all hover:bg-teal-700"
          >
            <Send size={18} className="translate-x-[1px] translate-y-[1px]" />
          </button>
        </form>
      </div>
    </div>
  );
}

function VaultView({ memories, toggleMemory }) {
  const tasks = memories.filter(m => (m.type === 'task' || m.type === 'reminder') && !m.completed);
  const notes = memories.filter(m => m.type === 'note' && !m.completed);
  const completed = memories.filter(m => m.completed);

  return (
    <div className="h-full overflow-y-auto p-6 space-y-8">
      <div className="flex items-center justify-between mb-2">
        <h2 className="text-2xl font-semibold text-slate-800 dark:text-slate-100 tracking-tight transition-colors duration-300">Memory Vault</h2>
        <span className="text-xs font-semibold text-teal-600 dark:text-teal-400 bg-teal-50 dark:bg-teal-900/30 px-2.5 py-1 rounded-md transition-colors duration-300">{memories.length} Items</span>
      </div>

      <VaultSection title="To Do & Reminders" items={tasks} toggle={toggleMemory} />
      <VaultSection title="Saved Notes" items={notes} toggle={toggleMemory} />
      <VaultSection title="Completed" items={completed} toggle={toggleMemory} />

      {memories.length === 0 && (
        <div className="text-center text-slate-400 dark:text-slate-500 mt-32 flex flex-col items-center transition-colors duration-300">
          <div className="w-20 h-20 bg-slate-100 dark:bg-slate-800 rounded-full flex items-center justify-center mb-6 transition-colors duration-300">
            <Inbox size={32} className="text-slate-300 dark:text-slate-600" />
          </div>
          <p className="text-lg font-medium text-slate-600 dark:text-slate-300">Your mind is clear.</p>
          <p className="text-sm mt-2 max-w-[200px]">Ask the assistant to remember things for you, and they will safely appear here.</p>
        </div>
      )}
    </div>
  );
}

function VaultSection({ title, items, toggle }) {
  if (!items || items.length === 0) return null;

  return (
    <div>
      <h3 className="text-[11px] font-bold tracking-widest text-slate-400 dark:text-slate-500 uppercase mb-3 px-1 transition-colors duration-300">{title}</h3>
      <div className="space-y-3">
        {items.map(item => (
          <div key={item.id} className={`group flex items-start p-4 rounded-2xl border shadow-sm transition-all duration-300 ${item.completed ? 'opacity-50 hover:opacity-100 bg-slate-50 dark:bg-slate-800/40 border-slate-100 dark:border-slate-800' : 'bg-white dark:bg-slate-800 border-slate-100 dark:border-slate-700 hover:shadow-md'}`}>
            
            {(item.type === 'task' || item.type === 'reminder' || item.completed) ? (
              <button onClick={() => toggle(item.id)} className="mt-0.5 mr-3 text-teal-600 dark:text-teal-500 flex-shrink-0 focus:outline-none">
                {item.completed ? <CheckCircle2 size={22} className="text-teal-500" /> : <Circle size={22} className="text-slate-300 dark:text-slate-600 hover:text-teal-400 dark:hover:text-teal-400 transition-colors" />}
              </button>
            ) : (
              <div className="mt-0.5 mr-3 text-slate-300 dark:text-slate-600 flex-shrink-0">
                <FileText size={20} />
              </div>
            )}
            
            <div className="flex-1 min-w-0 pr-2">
              <p className={`text-[15px] leading-snug break-words transition-colors duration-300 ${item.completed ? 'line-through text-slate-500 dark:text-slate-400' : 'text-slate-800 dark:text-slate-200'}`}>
                {item.content}
              </p>
              {item.dueDate && (
                <div className="flex items-center text-xs font-medium text-slate-400 dark:text-slate-400 mt-2 space-x-1.5 bg-slate-50 dark:bg-slate-700/50 inline-flex px-2 py-0.5 rounded-md transition-colors duration-300">
                  <Clock size={12} />
                  <span>{item.dueDate}</span>
                </div>
              )}
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

function CalendarView({ memories, toggleMemory }) {
  const [currentDate, setCurrentDate] = useState(new Date());
  const [selectedDate, setSelectedDate] = useState(new Date());

  const daysInMonth = new Date(currentDate.getFullYear(), currentDate.getMonth() + 1, 0).getDate();
  const firstDayOfMonth = new Date(currentDate.getFullYear(), currentDate.getMonth(), 1).getDay();

  const monthNames = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
  const dayNames = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];

  const prevMonth = () => setCurrentDate(new Date(currentDate.getFullYear(), currentDate.getMonth() - 1, 1));
  const nextMonth = () => setCurrentDate(new Date(currentDate.getFullYear(), currentDate.getMonth() + 1, 1));

  // Format YYYY-MM-DD for comparison
  const formatDate = (date) => {
    const d = new Date(date);
    let month = '' + (d.getMonth() + 1);
    let day = '' + d.getDate();
    const year = d.getFullYear();

    if (month.length < 2) month = '0' + month;
    if (day.length < 2) day = '0' + day;

    return [year, month, day].join('-');
  };

  const selectedIso = formatDate(selectedDate);
  const todaysIso = formatDate(new Date());

  const eventsOnSelectedDay = memories.filter(m => m.isoDate === selectedIso);

  const renderDays = () => {
    const days = [];
    for (let i = 0; i < firstDayOfMonth; i++) {
      days.push(<div key={`empty-${i}`} className="h-10"></div>);
    }
    for (let d = 1; d <= daysInMonth; d++) {
      const dateForDay = new Date(currentDate.getFullYear(), currentDate.getMonth(), d);
      const isoForDay = formatDate(dateForDay);
      const isSelected = isoForDay === selectedIso;
      const isToday = isoForDay === todaysIso;
      // Show dot indicator if there is at least one incomplete event that day
      const hasEvents = memories.some(m => m.isoDate === isoForDay && !m.completed);

      days.push(
        <button 
          key={d} 
          onClick={() => setSelectedDate(dateForDay)}
          className={`h-10 w-full flex flex-col items-center justify-center rounded-xl text-[15px] transition-all duration-200 relative
            ${isSelected ? 'bg-teal-600 text-white font-medium shadow-md' : 'hover:bg-slate-100 dark:hover:bg-slate-800 text-slate-700 dark:text-slate-300'}
            ${isToday && !isSelected ? 'border border-teal-500 font-semibold text-teal-600 dark:text-teal-400' : ''}
          `}
        >
          <span>{d}</span>
          {hasEvents && (
             <span className={`w-1.5 h-1.5 rounded-full absolute bottom-1.5 ${isSelected ? 'bg-white' : 'bg-teal-500 dark:bg-teal-400'}`}></span>
          )}
        </button>
      );
    }
    return days;
  };

  return (
    <div className="h-full flex flex-col overflow-hidden">
      {/* Calendar Grid Section */}
      <div className="p-6 pb-2 flex-none">
        <div className="flex items-center justify-between mb-6">
          <h2 className="text-2xl font-semibold text-slate-800 dark:text-slate-100 tracking-tight transition-colors duration-300">Calendar</h2>
          <div className="flex space-x-2">
            <button onClick={prevMonth} className="p-2 rounded-full bg-white dark:bg-slate-800 text-slate-600 dark:text-slate-300 shadow-sm border border-slate-100 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-700 transition-colors">
              <ChevronLeft size={18} />
            </button>
            <button onClick={nextMonth} className="p-2 rounded-full bg-white dark:bg-slate-800 text-slate-600 dark:text-slate-300 shadow-sm border border-slate-100 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-700 transition-colors">
              <ChevronRight size={18} />
            </button>
          </div>
        </div>
        
        <div className="mb-4 text-lg font-medium text-slate-700 dark:text-slate-200 transition-colors duration-300">
          {monthNames[currentDate.getMonth()]} {currentDate.getFullYear()}
        </div>

        <div className="grid grid-cols-7 gap-1 mb-2">
          {dayNames.map(day => (
            <div key={day} className="text-center text-[11px] font-bold tracking-wider text-slate-400 dark:text-slate-500 uppercase transition-colors duration-300">
              {day}
            </div>
          ))}
        </div>
        
        <div className="grid grid-cols-7 gap-y-1 gap-x-1">
          {renderDays()}
        </div>
      </div>

      {/* Selected Day Agenda Section */}
      <div className="flex-1 overflow-y-auto p-6 pt-4 border-t border-slate-100 dark:border-slate-800 mt-2 bg-slate-50/50 dark:bg-[#0f172a] transition-colors duration-300">
        <h3 className="text-[13px] font-semibold text-slate-500 dark:text-slate-400 mb-4 transition-colors duration-300">
          {selectedDate.toLocaleDateString(undefined, { weekday: 'long', month: 'long', day: 'numeric' })}
        </h3>
        
        {eventsOnSelectedDay.length > 0 ? (
          <div className="space-y-3">
            {eventsOnSelectedDay.map(item => (
              <div key={item.id} className={`flex items-start p-4 rounded-2xl border shadow-sm transition-colors duration-300 ${item.completed ? 'opacity-50 hover:opacity-100 bg-slate-50 dark:bg-slate-800/40 border-slate-100 dark:border-slate-800' : 'bg-white dark:bg-slate-800 border-slate-100 dark:border-slate-700 hover:shadow-md'}`}>
                <button onClick={() => toggleMemory(item.id)} className="mt-0.5 mr-3 text-teal-600 dark:text-teal-500 hover:text-teal-400 transition-colors flex-shrink-0 focus:outline-none">
                  {item.completed ? <CheckCircle2 size={22} className="text-teal-500" /> : <Circle size={22} className="text-slate-300 dark:text-slate-600 hover:text-teal-400 dark:hover:text-teal-400 transition-colors" />}
                </button>
                <div className="flex-1 min-w-0 pr-2">
                  <p className={`text-[15px] leading-snug break-words transition-colors duration-300 ${item.completed ? 'line-through text-slate-500 dark:text-slate-400' : 'text-slate-800 dark:text-slate-200'}`}>
                    {item.content}
                  </p>
                  {item.dueDate && (
                    <div className="flex items-center text-xs font-medium text-slate-400 dark:text-slate-400 mt-2 space-x-1.5 bg-slate-50 dark:bg-slate-700/50 inline-flex px-2 py-0.5 rounded-md transition-colors duration-300">
                      <Clock size={12} />
                      <span>{item.dueDate}</span>
                    </div>
                  )}
                </div>
              </div>
            ))}
          </div>
        ) : (
          <div className="text-center text-slate-400 dark:text-slate-600 mt-8 transition-colors duration-300">
            <CalendarIcon size={32} className="mx-auto mb-3 opacity-50" />
            <p className="text-sm">No events scheduled for this day.</p>
          </div>
        )}
      </div>
    </div>
  );
}
