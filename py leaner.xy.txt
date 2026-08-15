import { useState, useEffect, useRef } from "react";
import {
  Check, X, Lock, Star, Trophy, Sparkles, ChevronDown, RotateCcw,
  Flame, Terminal, Tag, Keyboard, GitBranch, Repeat, ListOrdered,
  Braces, HelpCircle, PartyPopper
} from "lucide-react";

const COLORS = {
  ink: "#20163B",
  paper: "#FBF7FF",
  paperAlt: "#F1E9FB",
  paperCard: "#FFFFFF",
  coral: "#FF6452",
  sun: "#FFC53D",
  mint: "#2FD6A7",
  sky: "#3DA9FC",
  grape: "#8C5CF5",
  rose: "#FF6FA8",
  muted: "#8B8398",
};

const PY_KEYWORDS = [
  "def", "return", "if", "elif", "else", "for", "while", "in", "range",
  "print", "True", "False", "None", "and", "or", "not", "import", "len", "input"
];

function highlightPy(code) {
  const lines = code.split("\n");
  const regex = /(#.*$)|("(?:[^"\\]|\\.)*"|'(?:[^'\\]|\\.)*')|(\b\d+(?:\.\d+)?\b)|([A-Za-z_][A-Za-z0-9_]*)|(\s+)|([^\sA-Za-z0-9_]+)/g;
  return lines.map((line, li) => {
    const tokens = [];
    let m;
    let key = 0;
    regex.lastIndex = 0;
    while ((m = regex.exec(line)) !== null) {
      const [full, comment, str, num, word] = m;
      let color = COLORS.ink;
      let weight = 400;
      if (comment) color = COLORS.muted;
      else if (str) color = COLORS.mint;
      else if (num) color = COLORS.sun;
      else if (word && PY_KEYWORDS.includes(word)) { color = COLORS.grape; weight = 700; }
      tokens.push(<span key={key++} style={{ color, fontWeight: weight }}>{full}</span>);
      if (m.index === regex.lastIndex) regex.lastIndex++;
    }
    return (
      <div key={li}>{tokens.length ? tokens : "\u00A0"}</div>
    );
  });
}

function CodeBlock({ code, filename = "lesson.py" }) {
  return (
    <div style={{ border: `3px solid ${COLORS.ink}`, borderRadius: 14, overflow: "hidden", boxShadow: `4px 4px 0 ${COLORS.ink}` }}>
      <div style={{ background: COLORS.ink, display: "flex", alignItems: "center", gap: 8, padding: "8px 12px" }}>
        <span style={{ width: 10, height: 10, borderRadius: 999, background: "#FF6452", display: "inline-block" }} />
        <span style={{ width: 10, height: 10, borderRadius: 999, background: "#FFC53D", display: "inline-block" }} />
        <span style={{ width: 10, height: 10, borderRadius: 999, background: "#2FD6A7", display: "inline-block" }} />
        <span style={{ color: "#C9C2E0", fontFamily: "'JetBrains Mono', monospace", fontSize: 12, marginLeft: 6 }}>{filename}</span>
      </div>
      <pre style={{ margin: 0, padding: "14px 16px", background: "#241A42", whiteSpace: "pre", overflowX: "auto" }}>
        <code style={{ fontFamily: "'JetBrains Mono', monospace", fontSize: 13.5, lineHeight: 1.7 }}>
          {highlightPy(code)}
        </code>
      </pre>
    </div>
  );
}

function OutputLine({ text }) {
  return (
    <div style={{ display: "flex", gap: 8, alignItems: "flex-start", marginTop: 10, fontFamily: "'JetBrains Mono', monospace", fontSize: 13, color: COLORS.ink }}>
      <span style={{ color: COLORS.mint, fontWeight: 700 }}>&gt;&gt;&gt;</span>
      <span style={{ whiteSpace: "pre-wrap" }}>{text}</span>
    </div>
  );
}

const LESSONS = [
  {
    id: "hello", num: 1, stage: "Fundamentals", title: "Say Hello", icon: Terminal, color: "coral",
    concept: "print() is Python's megaphone — anything inside the parentheses shows up on screen exactly as written. Wrap text in quotes so Python knows it's words, not code.",
    code: `print("Hello, world!")`,
    output: "Hello, world!",
    challenge: {
      prompt: "What does this print?",
      code: `print("Hello, " + "Python")`,
      options: ["Hello, Python", "Hello, + Python", "Hello Python", "Error"],
      answerIndex: 0,
      hint: "The + between two pieces of text glues them into one line — nothing extra gets added.",
      explain: "+ joins strings end-to-end, so the two pieces become one line: Hello, Python."
    }
  },
  {
    id: "variables", num: 2, stage: "Fundamentals", title: "Variables & Types", icon: Tag, color: "sky",
    concept: "A variable is a labeled box for a value. Python figures out the type on its own — text becomes a string, numbers become ints — no need to declare anything up front.",
    code: `name = "Ava"\nage = 14\nprint(name, "is", age)`,
    output: "Ava is 14",
    challenge: {
      prompt: "What does this print?",
      code: `x = 5\ny = "5"\nprint(x == y)`,
      options: ["True", "False", "5", "Error"],
      answerIndex: 1,
      hint: "== checks both the value and the type. One of these is a number; the other is text.",
      explain: "x is an int and y is a str — different types are never equal in Python, so the comparison is False."
    }
  },
  {
    id: "input", num: 3, stage: "Fundamentals", title: "Getting Input", icon: Keyboard, color: "grape",
    concept: "input() pauses your program and waits for someone to type something and hit enter. Whatever they type comes back to you — always as plain text.",
    code: `name = input("What's your name? ")\nprint("Hi, " + name + "!")`,
    output: "What's your name? _",
    challenge: {
      prompt: "input() always hands back a value of what type?",
      options: ["str (text)", "int (number)", "Whatever it looks like", "bool"],
      answerIndex: 0,
      hint: "Even if someone types 42, Python doesn't guess you meant a number.",
      explain: "input() always returns a string — to use it as a number, you convert it yourself with int() or float()."
    }
  },
  {
    id: "conditionals", num: 4, stage: "Logic & Flow", title: "Conditionals", icon: GitBranch, color: "mint",
    concept: "if / elif / else let your program choose a path. Python checks each condition top to bottom and runs the first block that's True — the rest get skipped.",
    code: `age = 16\nif age >= 18:\n    print("adult")\nelif age >= 13:\n    print("teen")\nelse:\n    print("kid")`,
    output: "teen",
    challenge: {
      prompt: "What prints if age = 16?",
      code: `age = 16\nif age >= 18:\n    print("adult")\nelif age >= 13:\n    print("teen")\nelse:\n    print("kid")`,
      options: ["adult", "teen", "kid", "Nothing"],
      answerIndex: 1,
      hint: "16 fails the first check but passes the second one.",
      explain: "16 isn't ≥ 18, so Python moves on. It is ≥ 13, so \"teen\" runs and else never fires."
    }
  },
  {
    id: "loops", num: 5, stage: "Logic & Flow", title: "Loops", icon: Repeat, color: "rose",
    concept: "A for loop repeats a block once per item. range(3) counts out three numbers starting at zero — a classic beginner trip-up worth memorizing early.",
    code: `for i in range(3):\n    print(i)`,
    output: "0\n1\n2",
    challenge: {
      prompt: "What does this print, in order?",
      code: `for i in range(3):\n    print(i)`,
      options: ["0, 1, 2", "1, 2, 3", "0, 1, 2, 3", "3"],
      answerIndex: 0,
      hint: "range(3) means \"three numbers\" — starting from zero, not from one.",
      explain: "range(3) generates 0, 1, 2 — three values total, always starting at zero."
    }
  },
  {
    id: "lists", num: 6, stage: "Data & Reuse", title: "Lists", icon: ListOrdered, color: "sun",
    concept: "A list holds many values in order, inside square brackets. Each item has an index starting at 0, so the first item is fruits[0] — not fruits[1].",
    code: `fruits = ["apple", "banana", "cherry"]\nprint(fruits[1])`,
    output: "banana",
    challenge: {
      prompt: "What does this print?",
      code: `fruits = ["apple", "banana", "cherry"]\nprint(fruits[1])`,
      options: ["apple", "banana", "cherry", "Error"],
      answerIndex: 1,
      hint: "Counting starts at 0, so index 1 is the second item, not the first.",
      explain: "Indexing starts at 0: fruits[0] is \"apple\" and fruits[1] is \"banana\"."
    }
  },
  {
    id: "functions", num: 7, stage: "Data & Reuse", title: "Functions", icon: Braces, color: "coral",
    concept: "def packages up a block of code you can reuse by name. return sends a value back to wherever the function was called — it doesn't print anything on its own.",
    code: `def greet(name):\n    return "Hey " + name + "!"\n\nprint(greet("Sam"))`,
    output: "Hey Sam!",
    challenge: {
      prompt: "What does this print?",
      code: `def greet(name):\n    return "Hey " + name + "!"\n\nprint(greet("Sam"))`,
      options: ["Hey Sam!", "None", "greet(Sam)", "Error"],
      answerIndex: 0,
      hint: "print() is wrapped around the function call — it prints whatever greet() returns.",
      explain: "greet(\"Sam\") returns \"Hey Sam!\", and print() displays exactly that."
    }
  },
  {
    id: "boss", num: 8, stage: "Boss Battle", title: "Final Challenge", icon: Trophy, color: "sun",
    concept: "Time to combine everything: a list, a loop, and math. total += s adds each score onto the running total, one lap of the loop at a time.",
    code: `scores = [88, 92, 79]\ntotal = 0\nfor s in scores:\n    total += s\nprint(total / len(scores))`,
    output: "86.33333333333333",
    challenge: {
      prompt: "What does the final print() output?",
      code: `scores = [88, 92, 79]\ntotal = 0\nfor s in scores:\n    total += s\nprint(total / len(scores))`,
      options: ["86.33333333333333", "86", "259", "Error"],
      answerIndex: 0,
      hint: "/ in Python always does true division — the result keeps its decimal places.",
      explain: "259 divided by 3 (len(scores)) is a float: 86.33333333333333 — / never rounds down on its own."
    }
  },
];

const STORAGE_KEY = "pyquest-progress-v1";

function todayStr() {
  return new Date().toISOString().slice(0, 10);
}
function defaultProgress() {
  return { completedIds: [], xp: 0, streak: 1, lastVisitDate: todayStr() };
}
function updateStreak(data) {
  const today = todayStr();
  if (!data.lastVisitDate) return { ...data, streak: 1, lastVisitDate: today };
  if (data.lastVisitDate === today) return data;
  const diffDays = Math.round((new Date(today) - new Date(data.lastVisitDate)) / 86400000);
  if (diffDays === 1) return { ...data, streak: (data.streak || 1) + 1, lastVisitDate: today };
  return { ...data, streak: 1, lastVisitDate: today };
}
async function loadProgress() {
  try {
    if (!window.storage) return null;
    const res = await window.storage.get(STORAGE_KEY, false);
    if (res && res.value) return JSON.parse(res.value);
  } catch (e) { /* nothing saved yet */ }
  return null;
}
async function saveProgress(data) {
  try {
    if (!window.storage) return;
    await window.storage.set(STORAGE_KEY, JSON.stringify(data), false);
  } catch (e) { /* best effort */ }
}
async function wipeProgress() {
  try {
    if (!window.storage) return;
    await window.storage.delete(STORAGE_KEY, false);
  } catch (e) { /* best effort */ }
}

function badgeStyle(color, filled) {
  const hex = COLORS[color] || COLORS.coral;
  return {
    background: filled ? hex : COLORS.paperCard,
    borderColor: COLORS.ink,
    boxShadow: `4px 4px 0 ${COLORS.ink}`,
  };
}

export default function PyQuest() {
  const [progress, setProgress] = useState(defaultProgress());
  const [loading, setLoading] = useState(true);
  const [activeId, setActiveId] = useState(null);
  const [attempt, setAttempt] = useState({ selected: null, wrongCount: 0, correct: false });
  const [toast, setToast] = useState(null);
  const [confirmReset, setConfirmReset] = useState(false);
  const trailRef = useRef(null);

  useEffect(() => {
    let mounted = true;
    (async () => {
      const loaded = await loadProgress();
      let data = loaded || defaultProgress();
      data = updateStreak(data);
      if (mounted) { setProgress(data); setLoading(false); }
      saveProgress(data);
    })();
    return () => { mounted = false; };
  }, []);

  useEffect(() => {
    setAttempt({ selected: null, wrongCount: 0, correct: false });
  }, [activeId]);

  const level = Math.floor(progress.xp / 100) + 1;
  const xpIntoLevel = progress.xp % 100;

  function isUnlocked(idx) {
    if (idx === 0) return true;
    return progress.completedIds.includes(LESSONS[idx - 1].id);
  }
  function isComplete(id) {
    return progress.completedIds.includes(id);
  }
  function showToast(msg) {
    setToast(msg);
    setTimeout(() => setToast(null), 2400);
  }
  function openLesson(idx) {
    if (!isUnlocked(idx)) {
      showToast("Finish the stop before this one first!");
      return;
    }
    setActiveId(LESSONS[idx].id);
  }
  function closeLesson() {
    setActiveId(null);
  }
  function selectOption(idx, lesson) {
    if (attempt.correct || isComplete(lesson.id)) return;
    const correct = idx === lesson.challenge.answerIndex;
    setAttempt((prev) => ({
      selected: idx,
      wrongCount: correct ? prev.wrongCount : prev.wrongCount + 1,
      correct,
    }));
  }
  function completeLesson(lesson) {
    if (isComplete(lesson.id)) { closeLesson(); return; }
    const gain = 20;
    setProgress((prev) => {
      const next = { ...prev, completedIds: [...prev.completedIds, lesson.id], xp: prev.xp + gain };
      saveProgress(next);
      return next;
    });
    showToast(`+${gain} XP — nice work!`);
    closeLesson();
  }
  function handleReset() {
    if (!confirmReset) { setConfirmReset(true); return; }
    wipeProgress();
    setProgress(defaultProgress());
    setConfirmReset(false);
    showToast("Progress reset — fresh trail ahead!");
  }

  const activeLesson = LESSONS.find((l) => l.id === activeId);
  const stageOrder = [];
  LESSONS.forEach((l) => { if (!stageOrder.includes(l.stage)) stageOrder.push(l.stage); });

  return (
    <div style={{ minHeight: "100vh", background: COLORS.paper, fontFamily: "'Plus Jakarta Sans', sans-serif", color: COLORS.ink, position: "relative", opacity: loading ? 0.5 : 1, transition: "opacity 0.4s ease" }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Fredoka:wght@500;600;700&family=Plus+Jakarta+Sans:wght@400;500;600;700;800&family=JetBrains+Mono:wght@400;500;700&display=swap');
        .pq-display { font-family: 'Fredoka', sans-serif; }
        .pq-mono { font-family: 'JetBrains Mono', monospace; }
        @keyframes pq-pulse { 0%,100% { box-shadow: 0 0 0 0 rgba(255,100,82,0.55); } 50% { box-shadow: 0 0 0 10px rgba(255,100,82,0); } }
        @keyframes pq-pop { 0% { transform: scale(0.85); opacity: 0; } 60% { transform: scale(1.04); opacity: 1; } 100% { transform: scale(1); opacity: 1; } }
        @keyframes pq-bounce { 0%,100% { transform: translateY(0); } 50% { transform: translateY(6px); } }
        @keyframes pq-toast-in { from { transform: translate(-50%, 20px); opacity: 0; } to { transform: translate(-50%, 0); opacity: 1; } }
        .pq-node-pulse { animation: pq-pulse 2.2s infinite; }
        .pq-pop { animation: pq-pop 0.35s ease-out; }
        .pq-bounce { animation: pq-bounce 1.6s ease-in-out infinite; }
        button:focus-visible, [tabindex]:focus-visible { outline: 3px solid ${COLORS.ink}; outline-offset: 2px; }
        @media (prefers-reduced-motion: reduce) {
          .pq-node-pulse, .pq-pop, .pq-bounce { animation: none !important; }
        }
      `}</style>

      {/* Sticky top bar */}
      <div style={{ position: "sticky", top: 0, zIndex: 30, background: "rgba(251,247,255,0.92)", borderBottom: `3px solid ${COLORS.ink}` }}>
        <div style={{ maxWidth: 980, margin: "0 auto", display: "flex", alignItems: "center", justifyContent: "space-between", padding: "10px 18px", gap: 12, flexWrap: "wrap" }}>
          <div className="pq-display" style={{ fontSize: 22, fontWeight: 700, display: "flex", alignItems: "center", gap: 6 }}>
            <span style={{ color: COLORS.coral }}>Py</span>Quest
          </div>
          <div style={{ display: "flex", alignItems: "center", gap: 10, flexWrap: "wrap" }}>
            <div style={{ display: "flex", alignItems: "center", gap: 6, background: COLORS.paperAlt, border: `2px solid ${COLORS.ink}`, borderRadius: 999, padding: "4px 10px" }}>
              <Flame size={16} color={COLORS.coral} fill={COLORS.coral} />
              <span className="pq-mono" style={{ fontSize: 13, fontWeight: 700 }}>{progress.streak}</span>
            </div>
            <div style={{ display: "flex", alignItems: "center", gap: 8, background: COLORS.paperAlt, border: `2px solid ${COLORS.ink}`, borderRadius: 999, padding: "4px 12px" }}>
              <Star size={16} color={COLORS.sun} fill={COLORS.sun} />
              <span className="pq-mono" style={{ fontSize: 13, fontWeight: 700 }}>Lvl {level}</span>
              <div style={{ width: 60, height: 6, borderRadius: 999, background: "#E4DBF7", overflow: "hidden" }}>
                <div style={{ width: `${xpIntoLevel}%`, height: "100%", background: COLORS.grape }} />
              </div>
            </div>
            <button
              onClick={handleReset}
              onBlur={() => setConfirmReset(false)}
              style={{ display: "flex", alignItems: "center", gap: 5, background: confirmReset ? COLORS.coral : "transparent", color: confirmReset ? "#fff" : COLORS.ink, border: `2px solid ${COLORS.ink}`, borderRadius: 999, padding: "5px 10px", fontSize: 12, fontWeight: 600, cursor: "pointer" }}
              className="pq-mono"
            >
              <RotateCcw size={13} />
              {confirmReset ? "Sure?" : "Reset"}
            </button>
          </div>
        </div>
      </div>

      {/* Hero */}
      <div style={{ position: "relative", overflow: "hidden", padding: "56px 20px 40px", textAlign: "center" }}>
        <div style={{ position: "absolute", top: -60, left: "50%", transform: "translateX(-160px)", width: 220, height: 220, borderRadius: "50%", background: COLORS.grape, opacity: 0.14, filter: "blur(2px)" }} />
        <div style={{ position: "absolute", top: -20, left: "50%", transform: "translateX(60px)", width: 180, height: 180, borderRadius: "50%", background: COLORS.sky, opacity: 0.16, filter: "blur(2px)" }} />
        {["def", "for i in range()", "==", "[ ]", ":", "#TODO", "return"].map((tok, i) => (
          <span
            key={tok}
            className="pq-mono"
            style={{
              position: "absolute",
              top: `${[8, 62, 20, 70, 40, 12, 55][i]}%`,
              left: `${[6, 10, 88, 84, 3, 92, 46][i]}%`,
              fontSize: 15,
              color: COLORS.ink,
              opacity: 0.08,
              transform: `rotate(${[-8, 6, 10, -6, -12, 8, -4][i]}deg)`,
              pointerEvents: "none",
            }}
          >
            {tok}
          </span>
        ))}
        <p className="pq-mono" style={{ color: COLORS.coral, fontWeight: 700, fontSize: 14, marginBottom: 10, position: "relative" }}># learn Python by playing</p>
        <h1 className="pq-display" style={{ fontSize: "clamp(40px, 8vw, 76px)", fontWeight: 700, lineHeight: 1.02, margin: 0, position: "relative" }}>
          Level up your <span style={{ color: COLORS.coral }}>Python</span>,<br />one stop at a time.
        </h1>
        <p style={{ maxWidth: 480, margin: "18px auto 26px", fontSize: 16, color: "#4B4166", position: "relative" }}>
          Eight bite-sized stops. Real code, real quizzes, real XP. No installs — just click a stop on the trail and go.
        </p>
        <button
          onClick={() => trailRef.current?.scrollIntoView({ behavior: "smooth", block: "start" })}
          className="pq-display"
          style={{
            position: "relative", fontSize: 16, fontWeight: 600, color: "#fff", background: COLORS.ink,
            border: `3px solid ${COLORS.ink}`, borderRadius: 999, padding: "12px 26px", cursor: "pointer",
            display: "inline-flex", alignItems: "center", gap: 8, boxShadow: `4px 4px 0 ${COLORS.coral}`
          }}
        >
          Start the Trail
          <ChevronDown size={18} className="pq-bounce" />
        </button>
      </div>

      {/* Trail */}
      <div ref={trailRef} style={{ position: "relative", maxWidth: 620, margin: "0 auto", padding: "20px 18px 60px" }}>
        <svg viewBox="0 0 100 100" preserveAspectRatio="none" style={{ position: "absolute", inset: 0, width: "100%", height: "100%", zIndex: 0 }}>
          <defs>
            <linearGradient id="pqTrailGrad" x1="0" y1="0" x2="0" y2="1">
              <stop offset="0%" stopColor={COLORS.coral} />
              <stop offset="25%" stopColor={COLORS.sky} />
              <stop offset="50%" stopColor={COLORS.grape} />
              <stop offset="75%" stopColor={COLORS.mint} />
              <stop offset="100%" stopColor={COLORS.sun} />
            </linearGradient>
          </defs>
          <path
            d="M50,0 C15,7 85,13 50,20 C15,27 85,33 50,40 C15,47 85,53 50,60 C15,67 85,73 50,80 C15,87 85,93 50,100"
            fill="none" stroke="url(#pqTrailGrad)" strokeWidth="1.6" strokeLinecap="round" opacity="0.35"
          />
        </svg>

        {LESSONS.map((lesson, idx) => {
          const unlocked = isUnlocked(idx);
          const complete = isComplete(lesson.id);
          const current = unlocked && !complete && (idx === 0 || isComplete(LESSONS[idx - 1].id));
          const Icon = lesson.icon;
          const align = idx % 2 === 0 ? "flex-start" : "flex-end";
          const showStageHeader = idx === 0 || LESSONS[idx - 1].stage !== lesson.stage;

          return (
            <div key={lesson.id} style={{ position: "relative", zIndex: 1 }}>
              {showStageHeader && (
                <div style={{ display: "flex", justifyContent: "center", margin: "28px 0 18px" }}>
                  <span className="pq-mono" style={{ background: COLORS.ink, color: "#fff", fontSize: 11, fontWeight: 700, letterSpacing: 1, textTransform: "uppercase", borderRadius: 999, padding: "6px 14px" }}>
                    Stage {stageOrder.indexOf(lesson.stage) + 1} · {lesson.stage}
                  </span>
                </div>
              )}
              <div style={{ display: "flex", justifyContent: align, marginBottom: 22 }}>
                <button
                  onClick={() => openLesson(idx)}
                  style={{
                    width: 220, display: "flex", flexDirection: "column", alignItems: "center", gap: 8,
                    background: "transparent", border: "none", cursor: unlocked ? "pointer" : "not-allowed",
                    opacity: unlocked ? 1 : 0.55, padding: 6,
                  }}
                >
                  <div style={{ position: "relative" }}>
                    <div
                      className={current ? "pq-node-pulse" : ""}
                      style={{
                        width: 76, height: 76, borderRadius: "50%", display: "flex", alignItems: "center", justifyContent: "center",
                        border: `4px solid ${COLORS.ink}`, ...badgeStyle(lesson.color, unlocked),
                      }}
                    >
                      {unlocked ? <Icon size={30} color={complete ? COLORS.ink : "#fff"} /> : <Lock size={26} color={COLORS.muted} />}
                    </div>
                    <span className="pq-mono" style={{
                      position: "absolute", top: -6, left: -6, width: 24, height: 24, borderRadius: "50%",
                      background: "#fff", border: `2px solid ${COLORS.ink}`, fontSize: 11, fontWeight: 700,
                      display: "flex", alignItems: "center", justifyContent: "center",
                    }}>{lesson.num}</span>
                    {complete && (
                      <span className="pq-pop" style={{
                        position: "absolute", bottom: -4, right: -4, width: 26, height: 26, borderRadius: "50%",
                        background: COLORS.mint, border: `2px solid ${COLORS.ink}`, display: "flex", alignItems: "center", justifyContent: "center",
                      }}>
                        <Check size={15} color="#fff" strokeWidth={3} />
                      </span>
                    )}
                  </div>
                  <span className="pq-display" style={{ fontSize: 15, fontWeight: 600, textAlign: "center" }}>{lesson.title}</span>
                </button>
              </div>
            </div>
          );
        })}

        <div style={{ textAlign: "center", marginTop: 30, position: "relative", zIndex: 1 }}>
          {progress.completedIds.length === LESSONS.length ? (
            <div className="pq-pop" style={{ display: "inline-flex", flexDirection: "column", alignItems: "center", gap: 8 }}>
              <PartyPopper size={34} color={COLORS.coral} />
              <span className="pq-display" style={{ fontSize: 22, fontWeight: 700 }}>Trail complete!</span>
              <span style={{ fontSize: 14, color: "#4B4166" }}>You cleared every stop — {progress.xp} XP earned.</span>
            </div>
          ) : (
            <span style={{ fontSize: 13, color: COLORS.muted }}>{progress.completedIds.length} / {LESSONS.length} stops cleared</span>
          )}
        </div>
      </div>

      {/* Lesson modal */}
      {activeLesson && (
        <div
          style={{ position: "fixed", inset: 0, zIndex: 50, background: "rgba(32,22,59,0.55)", display: "flex", alignItems: "center", justifyContent: "center", padding: 16 }}
          onClick={closeLesson}
        >
          <div
            className="pq-pop"
            onClick={(e) => e.stopPropagation()}
            style={{
              width: "100%", maxWidth: 520, maxHeight: "88vh", overflowY: "auto", background: COLORS.paperCard,
              border: `4px solid ${COLORS.ink}`, borderRadius: 20, boxShadow: `8px 8px 0 ${COLORS[activeLesson.color]}`,
              padding: 22,
            }}
          >
            <div style={{ display: "flex", alignItems: "flex-start", justifyContent: "space-between", marginBottom: 14 }}>
              <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
                <div style={{ width: 44, height: 44, borderRadius: "50%", background: COLORS[activeLesson.color], border: `3px solid ${COLORS.ink}`, display: "flex", alignItems: "center", justifyContent: "center" }}>
                  <activeLesson.icon size={22} color="#fff" />
                </div>
                <div>
                  <p className="pq-mono" style={{ fontSize: 11, color: COLORS.muted, margin: 0, textTransform: "uppercase", letterSpacing: 1 }}>Stop {activeLesson.num} · {activeLesson.stage}</p>
                  <h2 className="pq-display" style={{ fontSize: 22, fontWeight: 700, margin: 0 }}>{activeLesson.title}</h2>
                </div>
              </div>
              <button onClick={closeLesson} style={{ background: COLORS.paperAlt, border: `2px solid ${COLORS.ink}`, borderRadius: "50%", width: 32, height: 32, display: "flex", alignItems: "center", justifyContent: "center", cursor: "pointer", flexShrink: 0 }}>
                <X size={16} />
              </button>
            </div>

            <p style={{ fontSize: 14.5, lineHeight: 1.6, color: "#372B54", marginBottom: 14 }}>{activeLesson.concept}</p>
            <CodeBlock code={activeLesson.code} />
            <OutputLine text={activeLesson.output} />

            <div style={{ marginTop: 22, paddingTop: 18, borderTop: `2px dashed ${COLORS.paperAlt}` }}>
              {isComplete(activeLesson.id) ? (
                <>
                  <p className="pq-display" style={{ fontSize: 15, fontWeight: 600, marginBottom: 8, display: "flex", alignItems: "center", gap: 6 }}>
                    <Check size={16} color={COLORS.mint} /> You already cleared this stop
                  </p>
                  <p style={{ fontSize: 13.5, color: "#4B4166", marginBottom: 10 }}>{activeLesson.challenge.explain}</p>
                  <button onClick={closeLesson} style={{ background: COLORS.ink, color: "#fff", border: "none", borderRadius: 999, padding: "9px 18px", fontWeight: 600, cursor: "pointer" }} className="pq-display">
                    Back to trail
                  </button>
                </>
              ) : (
                <>
                  <p className="pq-display" style={{ fontSize: 15, fontWeight: 600, marginBottom: 10 }}>{activeLesson.challenge.prompt}</p>
                  {activeLesson.challenge.code && <div style={{ marginBottom: 12 }}><CodeBlock code={activeLesson.challenge.code} filename="quiz.py" /></div>}
                  <div style={{ display: "grid", gap: 8 }}>
                    {activeLesson.challenge.options.map((opt, i) => {
                      const isSelected = attempt.selected === i;
                      const showCorrect = attempt.correct && i === activeLesson.challenge.answerIndex;
                      const showWrong = isSelected && !attempt.correct;
                      let bg = COLORS.paperAlt, border = COLORS.ink, textColor = COLORS.ink;
                      if (showCorrect) { bg = COLORS.mint; textColor = "#fff"; }
                      else if (showWrong) { bg = COLORS.coral; textColor = "#fff"; }
                      return (
                        <button
                          key={i}
                          onClick={() => selectOption(i, activeLesson)}
                          className="pq-mono"
                          style={{
                            textAlign: "left", padding: "10px 14px", borderRadius: 12, border: `2px solid ${border}`,
                            background: bg, color: textColor, fontSize: 13.5, cursor: attempt.correct ? "default" : "pointer",
                          }}
                        >
                          {opt}
                        </button>
                      );
                    })}
                  </div>

                  {attempt.selected !== null && !attempt.correct && (
                    <div style={{ display: "flex", gap: 8, alignItems: "flex-start", marginTop: 12, background: "#FFF4E8", border: `2px solid ${COLORS.sun}`, borderRadius: 12, padding: "10px 12px" }}>
                      <HelpCircle size={16} color="#B8791A" style={{ flexShrink: 0, marginTop: 1 }} />
                      <p style={{ fontSize: 13, margin: 0, color: "#6B4A0E" }}>
                        {attempt.wrongCount >= 2 ? activeLesson.challenge.explain : activeLesson.challenge.hint}
                      </p>
                    </div>
                  )}

                  {attempt.correct && (
                    <div className="pq-pop" style={{ marginTop: 14 }}>
                      <div style={{ display: "flex", gap: 8, alignItems: "flex-start", background: "#E9FBF5", border: `2px solid ${COLORS.mint}`, borderRadius: 12, padding: "10px 12px", marginBottom: 12 }}>
                        <Sparkles size={16} color="#1B9E7A" style={{ flexShrink: 0, marginTop: 1 }} />
                        <p style={{ fontSize: 13, margin: 0, color: "#186B54" }}>{activeLesson.challenge.explain}</p>
                      </div>
                      <button
                        onClick={() => completeLesson(activeLesson)}
                        className="pq-display"
                        style={{ background: COLORS.ink, color: "#fff", border: "none", borderRadius: 999, padding: "10px 20px", fontWeight: 600, cursor: "pointer", boxShadow: `3px 3px 0 ${COLORS.sun}` }}
                      >
                        Continue the trail
                      </button>
                    </div>
                  )}
                </>
              )}
            </div>
          </div>
        </div>
      )}

      {/* Toast */}
      {toast && (
        <div
          style={{
            position: "fixed", bottom: 22, left: "50%", zIndex: 60, animation: "pq-toast-in 0.25s ease-out",
            background: COLORS.ink, color: "#fff", padding: "10px 20px", borderRadius: 999, fontSize: 13.5, fontWeight: 600,
          }}
          className="pq-display"
        >
          {toast}
        </div>
      )}

    </div>
  );
}
