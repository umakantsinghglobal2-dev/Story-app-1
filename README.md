# Story-app-1

<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>StoryBlank — Compact Interface</title>
<style>
  :root{
    --bg:#071025; --accent:#6ee7b7; --accent2:#7c6cff; --muted:#9aa4b2; --flash-bg:#facc15; --flash-color:#001019;
  }
  *{box-sizing:border-box}
  body{margin:0;height:100vh;font-family:Inter,system-ui,Arial;background:linear-gradient(180deg,var(--bg),#061827);color:#e6eef6;display:flex;flex-direction:column; overflow: hidden;}
  
  /* Compact header */
  header{display:flex;align-items:center;gap:8px;padding:8px;background:#061220; flex-wrap: wrap; position: sticky; top: 0; z-index: 50; min-height: 50px;}
  .title{font-weight:700; font-size: 16px;}
  
  /* More space for story area */
  .layout{display:flex;flex:1;gap:8px;padding:8px;min-height:0; height: calc(100vh - 100px);}
  .left{width:30%;display:flex;flex-direction:column;gap:6px;min-width:0; overflow-y: auto; height: 100%;}
  textarea#storyInput{min-height:80px;padding:8px;border-radius:6px;border:1px dashed rgba(255,255,255,0.04);background:transparent;color:inherit;resize:vertical; font-size: 14px;}
  .story-wrap{flex:1;display:flex;flex-direction:column;min-width:0; position: relative; max-width: 70%; height: 100%;}
  #storyArea{
    flex:1;
    border-radius:8px;
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    padding:12px;
    overflow:auto;
    outline:4px dashed rgba(126,156,255,0.06);
    position:relative; 
    height: 100%; 
    scroll-behavior: smooth;
    padding-bottom: 70px;
  }
  #story-container {
    position: relative;
    padding: 15px;
    padding-bottom: 30px;
  }
  
  .sentence{margin:10px 0;padding:6px 10px;border-radius:6px; transition: all 0.3s ease;}
  .sentence.activeSentence{
    background:rgba(255,255,255,0.05);
    box-shadow:inset 0 0 0 2px rgba(126,156,255,0.1);
    border-left: 3px solid #6ee7b7;
  }
  .sentence.completed-sentence {
    background: rgba(255, 193, 7, 0.3) !important;
    border-left: 3px solid #ffc107 !important;
  }
  .word{display:inline-block;padding:4px 6px;margin:0 2px;border-radius:4px;cursor:pointer;user-select:none; font-size: 15px; position: relative;}
  .word.marked{background:linear-gradient(90deg,var(--accent2),var(--accent));color:#001019}
  .word.blank{border-bottom:2px dashed rgba(255,255,255,0.15); color: rgba(255,255,255,0.85); background: rgba(0,0,0,0.2);}
  .word.temp-revealed{background:rgba(110, 231, 183, 0.2); color: #6ee7b7; border-bottom: 2px solid #6ee7b7;}
  .word.hint-blank{background: rgba(255,255,255,0.1); border: 1px solid rgba(255,255,255,0.2); font-family: monospace; letter-spacing: 1px; padding: 2px 4px;}
  .word.hint-correct{background: rgba(110, 231, 183, 0.3); color: #6ee7b7; border: 1px solid #6ee7b7;}
  .word.hint-incorrect{background: rgba(239, 68, 68, 0.3); color: #fca5a5; border: 1px solid #ef4444;}
  .word.completed-blank{background: #fff59d; color: #001019; border-bottom: 2px solid #ccc; font-weight: normal;}
  .underlined-mid{ text-decoration-line: underline; text-decoration-style: dotted; text-decoration-color: #7c6cff; background: rgba(124, 108, 255, 0.1); }
  .underlined-end{ text-decoration-line: underline; text-decoration-style: solid; text-decoration-color: #6ee7b7; background: rgba(110, 231, 183, 0.1); }
  .flash { animation: flashAnim 900ms ease-in-out; }
  @keyframes flashAnim { 0%{ background: var(--flash-bg); color: var(--flash-color) } 50%{ background: rgba(255,255,255,0.02); color: inherit } 100%{ background: var(--flash-bg); color: var(--flash-color) } }
  
  /* Compact footer */
  footer{
    display:flex;
    gap:8px;
    align-items:center;
    padding:8px;
    background:#071025;
    position:fixed;
    bottom:0;
    left:0;
    width:100%;
    z-index:60; 
    min-height: 50px;
    box-shadow: 0 -4px 12px rgba(0,0,0,0.3);
  }
  
  .gearPanel{position:absolute;top:46px;right:8px;background:#071025;border:1px solid rgba(255,255,255,0.04);padding:10px;border-radius:6px;display:none;z-index:80;width:300px; font-size: 14px;}
  #indicator{font-size:16px;opacity:0.35;transition:all 200ms ease}
  #indicator.active{opacity:1;color:#facc15;animation:pulse 1s infinite alternate}
  @keyframes pulse{from{transform:scale(1)}to{transform:scale(1.08)}}
  #revealBtn{
    position:fixed; 
    right:15px; 
    bottom:70px; 
    width:50px; 
    height:50px; 
    border-radius:9999px; 
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:22px;
    background:linear-gradient(45deg,var(--accent2),var(--accent)); 
    color:#001019; 
    box-shadow:0 8px 20px rgba(0,0,0,0.45); 
    cursor:pointer; 
    z-index:120; 
    transition:transform .18s ease;
  }
  #revealBtn.pulse{ animation:glow 1.2s infinite; }
  @keyframes glow {0%{transform:scale(1)}50%{transform:scale(1.06)}100%{transform:scale(1)}}
  
  .status-bar {display: flex; justify-content: space-between; padding: 6px; background: rgba(0,0,0,0.1); border-radius: 3px; margin-top: 6px; font-size: 12px;}
  .export-panel {background: rgba(255,255,255,0.03); padding: 10px; border-radius: 6px; margin-top: 10px; border: 1px dashed rgba(255,255,255,0.1); font-size: 14px;}
  .export-panel h3 {margin-top: 0; margin-bottom: 8px; color: var(--accent); font-size: 16px;}
  .export-buttons {display: flex; flex-wrap: wrap; gap: 6px;}
  .export-buttons button {flex: 1; min-width: 100px; font-size: 12px; padding: 6px 10px;}
  
  .notification {
    position: fixed;
    top: 15px;
    right: 15px;
    padding: 10px;
    background: var(--accent2);
    color: white;
    border-radius: 6px;
    box-shadow: 0 4px 10px rgba(0,0,0,0.2);
    z-index: 1000;
    opacity: 0;
    transform: translateY(-20px);
    transition: opacity 0.3s, transform 0.3s;
    max-width: 280px;
    text-align: center;
    font-size: 14px;
  }
  .notification.show {
    opacity: 1;
    transform: translateY(0);
  }
  
  .import-export-panel { background: rgba(255,255,255,0.03); padding: 10px; border-radius: 6px; margin-top: 10px; border: 1px dashed rgba(255,255,255,0.1); font-size: 14px;}
  .voice-test { margin-top: 8px; padding: 6px; background: rgba(255,255,255,0.05); border-radius: 4px; font-size: 14px;}
  
  /* Hint Mode Panel */
  .hint-mode-panel { 
    background: rgba(255,255,255,0.03); 
    padding: 10px; 
    border-radius: 6px; 
    margin-top: 10px; 
    border: 1px dashed rgba(255,255,255,0.1); 
    font-size: 14px; 
    flex: 1;
    min-height: 300px;
    display: flex;
    flex-direction: column;
    overflow-y: auto;
  }

  /* Tappable letters */
  .hint-letter { 
    display: inline-block; 
    padding: 6px 10px; 
    margin: 3px; 
    border-radius: 4px; 
    background: rgba(255,255,255,0.1); 
    cursor: pointer; 
    min-width: 35px; 
    text-align: center; 
    font-weight: bold; 
    font-size: 14px; 
    transition: all 0.3s ease; 
    position: relative;
  }
  .hint-letter.correct { background: #10b981; color: white; }
  .hint-letter.incorrect { background: #ef4444; color: white; }
  .hint-letter.selected { background: #3b82f6; color: white; }
  .hint-letter.default { background: rgba(255,255,255,0.1); color: #e6eef6; }
  .hint-letter:disabled { opacity: 0.5; cursor: not-allowed; }

  /* Pink asterisk for active letter */
  .hint-letter.active-letter::after {
    content: " ✱";
    color: #ff4db8;
    font-weight: bold;
    animation: glowPulse 1s infinite alternate;
    position: absolute;
    right: -15px;
    top: 0;
  }

  /* Sentence area */
  .hint-sentence-display { 
    margin: 10px 0; 
    padding: 10px; 
    background: rgba(0,0,0,0.2); 
    border-radius: 6px; 
    font-size: 14px; 
    user-select: none; 
    position: relative;
  }

  /* Active blank glowing pink asterisk ✱ */
  .word.active-hint-blank::after {
    content: " ✱";
    color: #ff4db8;
    font-weight: bold;
    animation: glowPulse 1s infinite alternate;
  }
  @keyframes glowPulse {
    from { text-shadow: 0 0 4px #ff4db8; }
    to   { text-shadow: 0 0 12px #ff66cc; }
  }

  .hint-controls { display: flex; gap: 6px; margin-top: 10px; flex-wrap: wrap; }
  .hint-stats { display: flex; justify-content: space-between; margin-top: 10px; font-size: 12px; flex-wrap: wrap; }
  .hint-analysis-panel { background: rgba(255,255,255,0.03); padding: 10px; border-radius: 6px; margin-top: 10px; border: 1px dashed rgba(255,255,255,0.1); display: none; font-size: 14px;}
  .analysis-item { margin: 6px 0; padding: 6px; background: rgba(0,0,0,0.2); border-radius: 3px; font-size: 14px;}
  
  /* File History Styles */
  .file-history-panel { background: rgba(255,255,255,0.03); padding: 10px; border-radius: 6px; margin-top: 10px; border: 1px dashed rgba(255,255,255,0.1); display: none; font-size: 14px;}
  .file-history-list { max-height: 150px; overflow-y: auto; margin: 8px 0; }
  .file-history-item { padding: 6px; margin: 3px 0; background: rgba(0,0,0,0.2); border-radius: 3px; cursor: pointer; font-size: 12px;}
  .file-history-item:hover { background: rgba(255,255,255,0.05); }
  
  /* Compact buttons */
  button {
    background: rgba(255,255,255,0.1);
    border: 1px solid rgba(255,255,255,0.2);
    color: #e6eef6;
    padding: 6px 12px;
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.2s ease;
    font-size: 12px;
    -webkit-user-select: none;
    user-select: none;
  }
  button:hover {
    background: rgba(255,255,255,0.2);
    border-color: rgba(255,255,255,0.3);
  }
  header button, footer button {
    background: rgba(124, 108, 255, 0.2);
    border: 1px solid rgba(124, 108, 255, 0.4);
    padding: 6px 10px;
    font-size: 12px;
  }
  header button:hover, footer button:hover {
    background: rgba(124, 108, 255, 0.3);
  }
  
  /* Make left panel content scrollable and give more space to hint mode */
  .left {
    min-height: 0;
    overflow-y: auto;
  }
  
  /* Scrollable controls area - contains everything except hint mode */
  .scrollable-controls {
    max-height: 40vh;
    min-height: 200px;
    overflow-y: auto;
    padding-right: 5px;
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 6px;
    padding: 8px;
    background: rgba(255,255,255,0.02);
    margin-bottom: 8px;
  }
  
  .scrollable-controls > * {
    margin-bottom: 8px;
  }
  
  /* Mode buttons container */
  .mode-buttons {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
    margin: 8px 0;
    padding: 8px;
    background: rgba(0,0,0,0.1);
    border-radius: 4px;
  }
  
  /* Hint letters container */
  #hintLettersContainer {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    max-height: 200px;
    overflow-y: auto;
    padding: 10px;
    background: rgba(0,0,0,0.2);
    border-radius: 6px;
    margin: 10px 0;
  }
  
  /* Collapsible panels */
  .panel-header { 
    display: flex; 
    justify-content: space-between; 
    align-items: center; 
    cursor: pointer; 
    padding: 6px;
    background: rgba(255,255,255,0.05);
    border-radius: 4px;
    margin-bottom: 5px;
  }
  .panel-content { 
    display: none;
    padding: 8px;
    background: rgba(0,0,0,0.1);
    border-radius: 4px;
  }
  .panel-content.expanded { 
    display: block;
  }
  
  /* Enhanced Hint Mode Tracking Styles */
  .word.active-hint-blank {
    border: 2px solid #3b82f6 !important;
    background: rgba(59, 130, 246, 0.1) !important;
  }
  
  .blank-stats {
    position: absolute;
    bottom: -12px;
    left: 0;
    right: 0;
    font-size: 9px;
    text-align: center;
    line-height: 1;
  }
  
  .correct-count {
    color: #10b981;
    margin-right: 4px;
  }
  
  .incorrect-count {
    color: #ef4444;
  }
  
  .performance-boxes {
    display: flex;
    gap: 6px;
    margin-top: 8px;
    flex-wrap: wrap;
  }
  
  .performance-box {
    padding: 3px 6px;
    border: 1px solid rgba(255,255,255,0.2);
    border-radius: 3px;
    font-size: 9px;
    background: rgba(0,0,0,0.3);
    text-align: center;
    min-width: 70px;
  }
  
  .performance-box .label {
    font-weight: bold;
    margin-bottom: 1px;
  }
  
  .performance-box .value {
    font-size: 10px;
  }
  
  @media (max-width:900px){ 
    .layout { flex-direction: column; height: auto; }
    .left{width:100%; max-width: 100% !important; height: auto;} 
    .story-wrap{max-width: 100% !important; width: 100%; height: 50vh;}
    #revealBtn{right:10px;bottom:64px} 
    header { flex-direction: column; align-items: flex-start; }
    .export-buttons, .import-export-buttons, .hint-controls { flex-direction: column; }
    header button, footer button { width: 100%; margin: 3px 0; }
    .scrollable-controls {
      max-height: 50vh;
    }
    .hint-mode-panel {
      min-height: 350px;
    }
    #storyArea {
      padding-bottom: 80px;
    }
    .performance-boxes {
      gap: 4px;
    }
    .performance-box {
      min-width: 65px;
      font-size: 8px;
    }
  }
</style>
</head>
<body>

<header>
  <div class="title">StoryBlank</div>
  <div style="margin-left:auto;display:flex;gap:6px;align-items:center;flex-wrap:wrap;">
    <div id="modeLabel" style="font-size: 12px;">Quiz</div>
  </div>
</header>

<div class="gearPanel" id="gearPanel">
  <div style="display:flex;flex-direction:column;gap:6px">
    <label>Repeat count <input id="repeatInput" type="number" min="1" max="10" value="2" style="width:60px;margin-left:6px; font-size: 12px;"></label>
    <label><input id="soundToggle" type="checkbox" checked> 🔊 Sound</label>
    <label><input id="autoJumpToggle" type="checkbox" checked> ↕️ Auto-Jump</label>
    <div style="display:flex;gap:6px;align-items:center">
      <label>Voice Speed <input id="voiceSpeedInput" type="number" min="0.5" max="2" step="0.1" value="1" style="width:60px;margin-left:6px; font-size: 12px;"></label>
    </div>
    <div class="voice-test">
      <button id="testVoiceBtn">Test Voice</button>
      <div id="voiceStatus" style="font-size:11px;margin-top:4px;"></div>
    </div>
    <div style="display:flex;gap:6px">
      <button id="helpBtn">Help</button>
      <button id="clearBtn">Clear</button>
    </div>
    <div class="help" style="color:var(--muted); font-size: 11px;">
      <strong>Button Explanations:</strong><br>
      • <strong>Walking Mode:</strong> Read-aloud with pauses at blanks<br>
      • <strong>Quiz Mode:</strong> Tap words to create blanks<br>
      • <strong>Hint Mode:</strong> Practice with initial letters<br>
      • <strong>Check Button:</strong> Validates letter selection (integrated into tapping)<br>
      • <strong>Settings:</strong> Adjust repetition, sound, auto-scroll, voice speed
    </div>
  </div>
</div>

<div class="layout">
  <div class="left">
    <!-- Scrollable controls area - contains textarea and all buttons -->
    <div class="scrollable-controls">
      <textarea id="storyInput" placeholder="Paste your story text here."></textarea>
      
      <!-- Mode buttons moved here -->
      <div class="mode-buttons">
        <button id="switchToWalkBtn">Walking</button>
        <button id="switchToQuizBtn">Quiz</button>
        <button id="switchToHintBtn">Hint</button>
        <button id="toggleModeBtn">Toggle</button>
        <button id="gearBtn">⚙️ Settings</button>
      </div>
      
      <!-- File operations -->
      <div style="display:flex;gap:6px;align-items:center;flex-wrap:wrap;">
        <input id="fileInput" type="file" accept=".txt,.json" style="display:none" />
        <button id="loadBtn">Load File</button>
        <button id="showHistoryBtn">Recent</button>
        <button id="loadFromTextBtn">Load Text</button>
      </div>
      
      <!-- Collapsible Controls Panel -->
      <div class="panel">
        <div class="panel-header" onclick="togglePanel('controlsPanel')">
          <span>Controls</span>
          <span id="controlsPanelIndicator">▼</span>
        </div>
        <div id="controlsPanel" class="panel-content">
          <div style="display:flex;gap:6px; flex-wrap: wrap;">
            <button id="startReadBtn">Start</button>
            <button id="stopReadBtn">Stop</button>
          </div>
        </div>
      </div>
      
      <!-- File History Panel -->
      <div class="panel">
        <div class="panel-header" onclick="togglePanel('fileHistoryPanel')">
          <span>Recent Files</span>
          <span id="fileHistoryPanelIndicator">▼</span>
        </div>
        <div id="fileHistoryPanel" class="panel-content">
          <div id="fileHistoryList" class="file-history-list">
            <!-- File history will be populated here -->
          </div>
          <button id="clearHistoryBtn" style="margin-top: 6px;">Clear History</button>
        </div>
      </div>
      
      <!-- Export Panel -->
      <div class="panel">
        <div class="panel-header" onclick="togglePanel('exportPanel')">
          <span>Export</span>
          <span id="exportPanelIndicator">▼</span>
        </div>
        <div id="exportPanel" class="panel-content">
          <div class="export-buttons">
            <button id="exportBlanksBtn">Blanks</button>
            <button id="exportAnswersBtn">Answers</button>
            <button id="copyToClipboardBtn">Copy</button>
            <button id="shareWhatsAppBtn">Share</button>
            <button id="exportAnalyticsBtn">Analytics</button>
          </div>
        </div>
      </div>
      
      <!-- Status Bar -->
      <div class="status-bar">
        <span id="wordCount">Words: 0</span>
        <span id="blankCount">Blanks: 0</span>
        <span id="repetitionStatus">Rep: 0/2</span>
      </div>
    </div>
    
    <!-- Hint Mode Panel - EXPANDED to use remaining space -->
    <div class="panel">
      <div class="panel-header" onclick="togglePanel('hintModePanel')">
        <span>Hint Mode</span>
        <span id="hintModePanelIndicator">▼</span>
      </div>
      <div id="hintModePanel" class="panel-content hint-mode-panel">
        
        <!-- Sentence Display -->
        <div id="hintSentenceDisplay" class="hint-sentence-display">
          <p>Select a sentence to start hint mode</p>
        </div>

        <!-- Progress Tracker -->
        <div id="hintProgress" style="font-size:12px; margin-bottom:10px; text-align:center; background:rgba(0,0,0,0.2); padding:6px; border-radius:4px;">
          Progress: 0/0
        </div>

        <!-- Tappable Letters -->
        <div id="hintLettersContainer"></div>

        <!-- Controls -->
        <div class="hint-controls">
          <button id="hintCheckBtn">Check</button>
          <button id="hintResetBtn">Reset</button>
          <button id="hintNextBtn">Next</button>
          <button id="hintPrevBtn">Prev</button>
        </div>

        <!-- Stats -->
        <div class="hint-stats">
          <span id="hintTimer">Time: 0s</span>
          <span id="hintAttempts">Attempts: 0</span>
          <span id="hintScore">Score: 0%</span>
        </div>

        <button id="showAnalysisBtn" style="margin-top: 6px;">Analysis</button>
      </div>
    </div>
    
    <!-- Analysis Panel -->
    <div id="hintAnalysisPanel" class="hint-analysis-panel">
      <h3>Hint Mode Analysis</h3>
      <div id="analysisContent"></div>
      <button id="closeAnalysisBtn">Close</button>
    </div>
  </div>

  <div class="story-wrap">
    <div id="storyArea">
      <div id="story-container">
        <div id="story"></div>
      </div>
    </div>
  </div>
</div>

<footer>
  <button id="prevBtn">⏪</button>
  <button id="playPauseBtn">▶</button>
  <button id="nextBtn">⏩</button>
  <button id="voicableSmallBtn" title="Reveal">🔊</button>
  <div style="margin-left:auto;display:flex;gap:10px;align-items:center">
    <div id="indicator" title="Reveal inactive">🔔</div>
    <div class="help" id="footerMode" style="font-size: 12px;">Quiz</div>
  </div>
</footer>

<div id="revealBtn" title="Reveal">🔊</div>

<audio id="ding" preload="auto">
  <source src="data:audio/mp3;base64,SUQzBAAAAAABEVRYWFgAAAAtAAADY29tbWVudABCaWdTb3VuZEJhbmsuY29tIC8gTGFTb25vdGhlcXVeLm9yZwBURU5DAAAAHQAAA1N3aXRjaCBQbHVzIMKpIE5DSCBTb2Z0d2FyZQBUSVQyAAAABgAAAzIyMzUAVFNTRQAAAA8AAANMyXZmNTcuODMuMTAwAAAAAAAAAAAAAAD/80DEAAAAA0gAAAAATEFNRTMuMTAwVVVVVVVVVVVVVUxBTUUzLjEwMFVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVf/zQsRbAAADSAAAAABVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVf/zQMSkAAADSAAAAABVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVV" type="audio/mp3">
</audio>

<div class="notification" id="notification" aria-live="polite"></div>

<script>
/* ----------------- State & helpers ----------------- */
const $ = id => document.getElementById(id);
let mode = 'quiz';
let storyData = [];
let currentSentenceIndex = 0;
let isPlaying = false;
let waitingForReveal = null;
let playPointer = { sentence:0, word:0 };
let revealResolve = null;
let currentRepetitionCount = 0;
let totalRepetitions = 2;

/* Enhanced Hint Mode State with tracking - FIXED: Remove redundant variables */
let hintModeState = {
  currentSentenceIndex: 0,
  currentBlankIndex: 0,
  selectedLetters: [],
  letterStates: {},
  startTime: null,
  attempts: 0,
  sentenceStats: [],
  timerInterval: null,
  wordRevealStates: [],
  blankStats: {},
  sentenceCompletion: {},
  sentenceTiming: {},
  totalHintTime: 0
};

/* Temporary reveal state for blanks */
let tempRevealState = {
  currentRevealedWord: null,
  lastRevealedWord: null
};

/* File History Management */
let fileHistory = {
  recentFiles: [],
  maxFiles: 20,
  currentPage: null
};

/* Large Storage Simulation */
class LargeStorage {
  constructor() {
    this.storageKey = 'storyblank_data';
    this.maxSize = 5 * 1024 * 1024 * 1024;
    this.init();
  }
  
  init() {
    if (!this.isStorageAvailable()) {
      console.warn('Large storage not available, using fallback');
      this.useFallback = true;
      this.fallbackData = {};
    } else {
      this.useFallback = false;
      this.loadData();
    }
  }
  
  isStorageAvailable() {
    try {
      const test = 'test';
      localStorage.setItem(test, test);
      localStorage.removeItem(test);
      return true;
    } catch (e) {
      return false;
    }
  }
  
  loadData() {
    try {
      const data = localStorage.getItem(this.storageKey);
      if (data) {
        const parsed = JSON.parse(data);
        if (parsed.hintModeState) hintModeState = {...hintModeState, ...parsed.hintModeState};
        if (parsed.storyData) storyData = parsed.storyData;
        if (parsed.mode) mode = parsed.mode;
        if (parsed.currentSentenceIndex !== undefined) currentSentenceIndex = parsed.currentSentenceIndex;
        if (parsed.fileHistory) fileHistory = {...fileHistory, ...parsed.fileHistory};
        if (parsed.currentPage) fileHistory.currentPage = parsed.currentPage;
      }
    } catch (e) {
      console.error('Error loading data:', e);
    }
  }
  
  saveData() {
    try {
      const data = {
        storyData,
        mode,
        currentSentenceIndex,
        hintModeState,
        fileHistory,
        currentPage: {
          mode: mode,
          sentenceIndex: mode === 'hint' ? hintModeState.currentSentenceIndex : currentSentenceIndex,
          timestamp: Date.now()
        },
        timestamp: Date.now()
      };
      
      const dataString = JSON.stringify(data);
      
      if (new Blob([dataString]).size > this.maxSize) {
        this.cleanupOldData();
      }
      
      if (!this.useFallback) {
        localStorage.setItem(this.storageKey, dataString);
      } else {
        this.fallbackData = data;
      }
    } catch (e) {
      console.error('Error saving data:', e);
    }
  }
  
  cleanupOldData() {
    try {
      const data = localStorage.getItem(this.storageKey);
      if (data) {
        const parsed = JSON.parse(data);
        const essentialData = {
          storyData: parsed.storyData,
          mode: parsed.mode,
          currentSentenceIndex: parsed.currentSentenceIndex,
          hintModeState: parsed.hintModeState,
          fileHistory: parsed.fileHistory,
          currentPage: parsed.currentPage,
          timestamp: Date.now()
        };
        localStorage.setItem(this.storageKey, JSON.stringify(essentialData));
      }
    } catch (e) {
      console.error('Error cleaning up data:', e);
    }
  }
}

const largeStorage = new LargeStorage();

/* Panel toggle function */
function togglePanel(panelId) {
  const panel = $(panelId);
  const indicator = $(panelId + 'Indicator');
  if (panel.classList.contains('expanded')) {
    panel.classList.remove('expanded');
    indicator.textContent = '▼';
  } else {
    panel.classList.add('expanded');
    indicator.textContent = '▲';
  }
}

/* notification */
function notify(msg, t=1600){
  const n = $('notification');
  n.textContent = msg;
  n.classList.add('show');
  clearTimeout(n._tout);
  n._tout = setTimeout(()=>n.classList.remove('show'), t);
}

/* File History Functions */
function addToFileHistory(fileName, content) {
  fileHistory.recentFiles = fileHistory.recentFiles.filter(file => file.name !== fileName);
  fileHistory.recentFiles.unshift({
    name: fileName,
    content: content,
    timestamp: Date.now(),
    size: content.length
  });
  
  if (fileHistory.recentFiles.length > fileHistory.maxFiles) {
    fileHistory.recentFiles = fileHistory.recentFiles.slice(0, fileHistory.maxFiles);
  }
  
  renderFileHistory();
  largeStorage.saveData();
}

function renderFileHistory() {
  const historyList = $('fileHistoryList');
  if (!historyList) return;
  
  historyList.innerHTML = '';
  
  if (fileHistory.recentFiles.length === 0) {
    historyList.innerHTML = '<p>No recent files</p>';
    return;
  }
  
  fileHistory.recentFiles.forEach((file, index) => {
    const item = document.createElement('div');
    item.className = 'file-history-item';
    item.innerHTML = `
      <strong>${file.name}</strong>
      <div style="font-size:11px;color:var(--muted);">
        ${new Date(file.timestamp).toLocaleDateString()} - 
        ${Math.ceil(file.size / 1024)}KB
      </div>
    `;
    item.onclick = () => loadFileFromHistory(file);
    historyList.appendChild(item);
  });
}

function loadFileFromHistory(file) {
  parseAndLoadText(file.content);
  notify(`Loaded from history: ${file.name}`);
}

function clearFileHistory() {
  fileHistory.recentFiles = [];
  renderFileHistory();
  largeStorage.saveData();
  notify('File history cleared');
}

/* TTS wrapper */
function speakText(text){
  return new Promise(res => {
    if(!('speechSynthesis' in window) || !text){ 
      res(); 
      return; 
    }
    
    if (window.speechSynthesis.speaking) {
      window.speechSynthesis.cancel();
    }
    
    const u = new SpeechSynthesisUtterance(String(text));
    u.rate = parseFloat($('voiceSpeedInput').value) || 1;
    u.onend = () => res();
    u.onerror = () => res();
    
    try { 
      window.speechSynthesis.speak(u); 
    } catch (error) { 
      res(); 
    }
  });
}

function wait(ms){ return new Promise(r => setTimeout(r, ms)); }

/* ---------------- Parse & render ---------------- */
function parseAndLoadText(text){
  if(!text) return;
  const sents = text.trim().split(/(?<=\.|\?|!)\s+|\n+/).filter(Boolean);
  storyData = sents.map(s => s.split(/\s+/).filter(Boolean).map(w => ({ text: w.replace(/\s+/g,''), marked:false, state:'unmarked', spoken:false })));
  totalRepetitions = parseInt(localStorage.getItem('sb_repeatCount') || $('repeatInput').value || '2');
  currentSentenceIndex = 0;
  playPointer = { sentence:0, word:0 };
  currentRepetitionCount = 0;
  
  hintModeState.sentenceStats = storyData.map((sent, index) => ({
    sentenceIndex: index,
    attempts: 0,
    bestTime: null,
    averageTime: 0,
    wrongLetters: [],
    correctLetters: [],
    completed: false
  }));
  
  // Initialize blank stats for all sentences
  storyData.forEach((sent, sidx) => {
    hintModeState.blankStats[sidx] = {};
    const markedIndices = sent.map((word, widx) => word.marked ? widx : -1).filter(i => i !== -1);
    markedIndices.forEach((widx, blankIndex) => {
      hintModeState.blankStats[sidx][blankIndex] = { correct: 0, incorrect: 0 };
    });
    
    // Initialize timing for sentence
    hintModeState.sentenceTiming[sidx] = {
      correctTime: 0,
      incorrectTime: 0,
      completions: 0,
      minTime: null
    };
  });
  
  updateCounts();
  render();
  $('storyArea').scrollTop = 0;
  largeStorage.saveData();
}

function resetSentenceToBlankState(sidx){
  const sent = storyData[sidx]; if(!sent) return;
  sent.forEach(w => { if(w.marked){ w.state = 'blank'; w.spoken = false; }});
}

function resetNonActiveSentencesToBlankState() {
  storyData.forEach((sent, sidx) => {
    if (sidx !== currentSentenceIndex) {
      sent.forEach(w => { 
        if (w.marked && w.state && w.state.startsWith('underlined')) { 
          w.state = 'blank'; 
          w.spoken = false; 
        } 
      });
    }
  });
}

function getContiguousGroup(sidx, widx){
  const sent = storyData[sidx], g = [widx];
  for(let i = widx - 1; i >= 0; i--){ if(sent[i].marked) g.unshift(i); else break; }
  for(let i = widx + 1; i < sent.length; i++){ if(sent[i].marked) g.push(i); else break; }
  return g;
}

function classifyGroupAndSetStates(sidx, group){
  const sent = storyData[sidx];
  for(let k = 0; k < group.length; k++){ const idx = group[k]; sent[idx].state = (k === group.length - 1) ? 'underlined-end' : 'underlined-mid'; sent[idx].spoken = false; }
}

function flashSpans(sidx, idxs){
  idxs.forEach(i => {
    const el = document.querySelector('.word[data-sidx="'+sidx+'"][data-widx="'+i+'"]');
    if(!el) return;
    el.classList.add('flash');
    setTimeout(() => el.classList.remove('flash'), 900);
  });
}

/* MODIFIED: Now reveals only one blank at a time in walking mode */
function revealNextBlankImmediate(){
  const sidx = currentSentenceIndex; 
  const sent = storyData[sidx]; 
  if(!sent) return false;
  
  // Find the very next single blank (not contiguous group)
  const nb = sent.findIndex(w => w.marked && w.state === 'blank'); 
  if(nb === -1) return false;
  
  // In walking mode, reveal only this single blank
  if (mode === 'walk') {
    sent[nb].state = 'underlined-end'; 
    sent[nb].spoken = false;
    // Flash only this single word
    flashSpans(sidx, [nb]);
  } else {
    // Original behavior for other modes
    const g = getContiguousGroup(sidx, nb); 
    classifyGroupAndSetStates(sidx, g); 
    flashSpans(sidx, g);
  }
  
  waitingForReveal = null; 
  setIndicator(false); 
  render(); 
  return true;
}

function setIndicator(on){
  const ind = $('indicator'), rbtn = $('revealBtn');
  if(on){ 
    ind.classList.add('active'); 
    rbtn.classList.add('pulse'); 
    if($('soundToggle').checked){ 
      const d = $('ding'); 
      try{ 
        d.currentTime = 0; 
        d.play().catch(() => {});
      }catch(e){}
    } 
  } else { 
    ind.classList.remove('active'); 
    rbtn.classList.remove('pulse'); 
  }
}

function waitForReveal(){
  return new Promise(res => { revealResolve = res; });
}

function notifyRevealFromUI(){
  const ok = revealNextBlankImmediate();
  if(revealResolve){ const r = revealResolve; revealResolve = null; r(); }
  return ok;
}

/* Blank Tapping Features */
function handleBlankTap(sidx, widx) {
  const word = storyData[sidx][widx];
  if (!word.marked) return;
  
  if (tempRevealState.currentRevealedWord && 
      tempRevealState.currentRevealedWord.sentenceIndex === sidx && 
      tempRevealState.currentRevealedWord.wordIndex === widx) {
    tempRevealState.lastRevealedWord = {...tempRevealState.currentRevealedWord};
    tempRevealState.currentRevealedWord = null;
  } else {
    if (tempRevealState.currentRevealedWord) {
      tempRevealState.lastRevealedWord = {...tempRevealState.currentRevealedWord};
    }
    tempRevealState.currentRevealedWord = {sentenceIndex: sidx, wordIndex: widx};
  }
  
  render();
}

function resetAllTempReveals() {
  tempRevealState.currentRevealedWord = null;
  tempRevealState.lastRevealedWord = null;
  render();
}

/* ---------------- Enhanced Hint Mode Functions ---------------- */
function initHintMode() {
  if (storyData.length === 0) {
    notify('No story loaded for hint mode');
    return;
  }
  
  hintModeState.currentBlankIndex = 0;
  hintModeState.selectedLetters = [];
  hintModeState.letterStates = {};
  hintModeState.startTime = Date.now();
  hintModeState.attempts = 0;
  
  // Initialize word reveal states for current sentence
  const sentence = storyData[hintModeState.currentSentenceIndex];
  hintModeState.wordRevealStates = sentence.map((word, index) => ({
    revealed: false,
    correct: false,
    incorrect: false,
    wordIndex: index
  }));
  
  if (hintModeState.timerInterval) {
    clearInterval(hintModeState.timerInterval);
  }
  hintModeState.timerInterval = setInterval(updateHintTimer, 1000);
  
  renderHintMode();
  render();
}

function renderHintMode() {
  const sentence = storyData[hintModeState.currentSentenceIndex];
  if (!sentence) return;
  
  // Get marked words only
  const markedWords = sentence.filter(word => word.marked);
  
  if (markedWords.length === 0) {
    $('hintSentenceDisplay').innerHTML = '<p>No marked words in this sentence. Switch to Quiz mode to mark words.</p>';
    $('hintLettersContainer').innerHTML = '';
    return;
  }
  
  // Display sentence with blanks - with active blank highlighting
  let blankCount = 0;
  const displaySentenceHTML = sentence.map((word, i) => {
    if (word.marked) {
      // FIXED: Use currentBlankIndex as single source of truth
      const isActive = blankCount === hintModeState.currentBlankIndex;
      const wordRevealState = hintModeState.wordRevealStates[i];
      const isCompleted = wordRevealState && wordRevealState.revealed && wordRevealState.correct;
      
      let blankHTML;
      if (isCompleted) {
        blankHTML = `<span class="word hint-blank completed-blank">${word.text}</span>`;
      } else {
        blankHTML = `<span class="word hint-blank ${isActive ? 'active-hint-blank' : ''}" data-blank-index="${blankCount}">____</span>`;
      }
      blankCount++;
      return blankHTML;
    }
    return word.text;
  }).join(' ');
  
  $('hintSentenceDisplay').innerHTML = `
    <p><strong>Sentence ${hintModeState.currentSentenceIndex + 1}:</strong> ${displaySentenceHTML}</p>
    <p style="font-size:12px; color:var(--muted); margin-top:8px;">Tap letters below to fill blanks</p>
  `;
  
  // Get initial letters of marked words
  const initialLetters = markedWords.map(word => word.text.charAt(0).toUpperCase());
  
  // Create shuffled letters for tappable options
  const shuffledLetters = shuffleArray([...initialLetters]);
  
  const lettersContainer = $('hintLettersContainer');
  lettersContainer.innerHTML = '<p style="font-size:12px; margin-bottom:8px;">Available letters:</p>';
  
  shuffledLetters.forEach((letter, index) => {
    const letterBtn = document.createElement('div');
    
    // FIXED: Disable letters that have been selected
    const isDisabled = hintModeState.selectedLetters.includes(letter);
    
    letterBtn.className = `hint-letter ${hintModeState.letterStates[letter] || 'default'}`;
    letterBtn.textContent = letter;
    letterBtn.dataset.letter = letter;
    letterBtn.dataset.index = index;
    if (isDisabled) {
      letterBtn.disabled = true;
      letterBtn.style.opacity = '0.5';
      letterBtn.style.cursor = 'not-allowed';
    }
    letterBtn.onclick = (e) => {
      e.stopPropagation();
      if (!isDisabled) {
        selectHintLetter(letter, letterBtn);
      }
    };
    lettersContainer.appendChild(letterBtn);
  });
  
  updateHintStats();
}

function selectHintLetter(letter, element) {
  const sentence = storyData[hintModeState.currentSentenceIndex];
  const markedWords = sentence.filter(word => word.marked);
  
  if (hintModeState.currentBlankIndex >= markedWords.length) return;

  const currentMarkedWord = markedWords[hintModeState.currentBlankIndex];
  const correctLetter = currentMarkedWord.text.charAt(0).toUpperCase();
  const wordIndex = findMarkedWordIndex(hintModeState.currentSentenceIndex, hintModeState.currentBlankIndex);
  
  const sidx = hintModeState.currentSentenceIndex;
  const blankIndex = hintModeState.currentBlankIndex;

  // FIXED: Add to selected letters
  hintModeState.selectedLetters.push(letter);

  // Check correctness
  if (letter === correctLetter) {
    element.classList.remove('default', 'incorrect');
    element.classList.add('correct');
    hintModeState.letterStates[letter] = 'correct';

    if (!hintModeState.blankStats[sidx]) hintModeState.blankStats[sidx] = {};
    if (!hintModeState.blankStats[sidx][blankIndex]) hintModeState.blankStats[sidx][blankIndex] = { correct: 0, incorrect: 0 };
    hintModeState.blankStats[sidx][blankIndex].correct++;

    // Mark reveal
    hintModeState.wordRevealStates[wordIndex] = { revealed: true, correct: true, incorrect: false, wordIndex };
    hintModeState.currentBlankIndex++;

  } else {
    element.classList.remove('default', 'correct');
    element.classList.add('incorrect');
    hintModeState.letterStates[letter] = 'incorrect';

    if (!hintModeState.blankStats[sidx]) hintModeState.blankStats[sidx] = {};
    if (!hintModeState.blankStats[sidx][blankIndex]) hintModeState.blankStats[sidx][blankIndex] = { correct: 0, incorrect: 0 };
    hintModeState.blankStats[sidx][blankIndex].incorrect++;

    hintModeState.wordRevealStates[wordIndex] = { revealed: true, correct: false, incorrect: true, wordIndex };
  }

  // Scroll sentence in story area into view
  const storyElement = document.querySelector(`.sentence[data-sidx="${sidx}"]`);
  if (storyElement) {
    setTimeout(() => storyElement.scrollIntoView({ behavior: "smooth", block: "center" }), 50);
  }

  render();
  
  // Check completion
  if (hintModeState.currentBlankIndex >= markedWords.length) {
    const allCorrect = markedWords.every((word, index) => {
      const wordIdx = findMarkedWordIndex(hintModeState.currentSentenceIndex, index);
      return hintModeState.wordRevealStates[wordIdx]?.correct;
    });
    
    if (allCorrect) {
      // Mark sentence as completed
      hintModeState.sentenceCompletion[sidx] = true;
      
      // Update completion stats
      hintModeState.sentenceTiming[sidx].completions++;
      
      const totalTime = hintModeState.sentenceTiming[sidx].correctTime + hintModeState.sentenceTiming[sidx].incorrectTime;
      if (!hintModeState.sentenceTiming[sidx].minTime || totalTime < hintModeState.sentenceTiming[sidx].minTime) {
        hintModeState.sentenceTiming[sidx].minTime = totalTime;
      }
      
      notify('Congratulations! All blanks filled correctly!');
      const stats = hintModeState.sentenceStats[hintModeState.currentSentenceIndex];
      stats.completed = true;
      stats.attempts = hintModeState.attempts;
    }
  }
}

function findMarkedWordIndex(sentenceIndex, blankIndex) {
  const sentence = storyData[sentenceIndex];
  let markedCount = 0;
  
  for (let i = 0; i < sentence.length; i++) {
    if (sentence[i].marked) {
      if (markedCount === blankIndex) {
        return i;
      }
      markedCount++;
    }
  }
  return -1;
}

function resetHintSentence() {
  hintModeState.currentBlankIndex = 0;
  hintModeState.selectedLetters = [];
  hintModeState.letterStates = {};
  hintModeState.startTime = Date.now();
  
  // Reset word reveal states
  const sentence = storyData[hintModeState.currentSentenceIndex];
  hintModeState.wordRevealStates = sentence.map((word, index) => ({
    revealed: false,
    correct: false,
    incorrect: false,
    wordIndex: index
  }));
  
  renderHintMode();
  render();
}

function nextHintSentence() {
  if (hintModeState.currentSentenceIndex < storyData.length - 1) {
    hintModeState.currentSentenceIndex++;
    initHintMode();
    render();
  } else {
    notify('No more sentences');
  }
}

function prevHintSentence() {
  if (hintModeState.currentSentenceIndex > 0) {
    hintModeState.currentSentenceIndex--;
    initHintMode();
    render();
  } else {
    notify('This is the first sentence');
  }
}

function updateHintTimer() {
  if (hintModeState.startTime) {
    const timeSpent = Math.round((Date.now() - hintModeState.startTime) / 1000);
    $('hintTimer').textContent = `Time: ${timeSpent}s`;
    
    // Update total hint time
    hintModeState.totalHintTime = timeSpent;
  }
}

function updateHintStats() {
  const stats = hintModeState.sentenceStats[hintModeState.currentSentenceIndex];
  $('hintAttempts').textContent = `Attempts: ${hintModeState.attempts}`;
  
  const totalSentences = storyData.length;
  const completedSentences = Object.keys(hintModeState.sentenceCompletion).length;
  const score = totalSentences > 0 ? Math.round((completedSentences / totalSentences) * 100) : 0;
  $('hintScore').textContent = `Score: ${score}%`;
}

function showHintAnalysis() {
  const analysisContent = $('analysisContent');
  analysisContent.innerHTML = '';
  
  // Calculate total time across all sentences
  let totalCorrectTime = 0;
  let totalIncorrectTime = 0;
  let totalCompletions = 0;
  
  Object.keys(hintModeState.sentenceTiming).forEach(sidx => {
    const timing = hintModeState.sentenceTiming[sidx];
    totalCorrectTime += timing.correctTime || 0;
    totalIncorrectTime += timing.incorrectTime || 0;
    totalCompletions += timing.completions || 0;
  });
  
  analysisContent.innerHTML += `
    <div class="analysis-item">
      <h4>Overall Hint Mode Statistics</h4>
      <p>Total Time in Hint Mode: ${Math.round(hintModeState.totalHintTime)} seconds</p>
      <p>Total Correct Tap Time: ${Math.round(totalCorrectTime / 1000)} seconds</p>
      <p>Total Incorrect Tap Time: ${Math.round(totalIncorrectTime / 1000)} seconds</p>
      <p>Total Sentence Completions: ${totalCompletions}</p>
    </div>
  `;
  
  hintModeState.sentenceStats.forEach((stats, index) => {
    if (stats.attempts > 0 || hintModeState.sentenceCompletion[index]) {
      const analysisItem = document.createElement('div');
      analysisItem.className = 'analysis-item';
      
      const timing = hintModeState.sentenceTiming[index] || { correctTime: 0, incorrectTime: 0, completions: 0, minTime: null };
      
      let analysisHTML = `
        <h4>Sentence ${index + 1}</h4>
        <p>Completed: ${hintModeState.sentenceCompletion[index] ? 'Yes' : 'No'}</p>
        <p>Attempts: ${stats.attempts}</p>
        <p>Total Correct Time: ${Math.round(timing.correctTime / 1000)}s</p>
        <p>Total Incorrect Time: ${Math.round(timing.incorrectTime / 1000)}s</p>
        <p>Completions: ${timing.completions}</p>
        <p>Minimum Completion Time: ${timing.minTime ? Math.round(timing.minTime / 1000) + 's' : 'N/A'}</p>
      `;
      
      // Show blank-specific stats
      if (hintModeState.blankStats[index]) {
        analysisHTML += `<p style="margin-top: 8px;"><strong>Blank Statistics:</strong></p>`;
        Object.keys(hintModeState.blankStats[index]).forEach(blankIndex => {
          const blankStat = hintModeState.blankStats[index][blankIndex];
          analysisHTML += `
            <p style="font-size: 12px; margin: 2px 0;">
              Blank ${parseInt(blankIndex) + 1}: 
              <span style="color: #10b981;">${blankStat.correct} correct</span>, 
              <span style="color: #ef4444;">${blankStat.incorrect} incorrect</span>
            </p>
          `;
        });
      }
      
      analysisItem.innerHTML = analysisHTML;
      analysisContent.appendChild(analysisItem);
    }
  });
  
  $('hintAnalysisPanel').style.display = 'block';
}

function shuffleArray(array) {
  const newArray = [...array];
  for (let i = newArray.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [newArray[i], newArray[j]] = [newArray[j], newArray[i]];
  }
  return newArray;
}

/* Enhanced center function with smooth scrolling */
function centerActiveSentence(sidx, smooth = false) {
  const storyArea = $('storyArea');
  const el = document.querySelector('.sentence[data-sidx="' + sidx + '"]');
  
  if (!el || !storyArea) return;
  
  const sentenceTop = el.offsetTop;
  const sentenceHeight = el.offsetHeight;
  const containerHeight = storyArea.offsetHeight;
  
  // Calculate scroll position to ensure the entire sentence is visible
  let scrollPosition;
  
  // If sentence is taller than container, show from the top
  if (sentenceHeight > containerHeight) {
    scrollPosition = sentenceTop;
  } 
  // Otherwise center the sentence, but ensure it doesn't get cut off at the bottom
  else {
    scrollPosition = sentenceTop - (containerHeight / 2) + (sentenceHeight / 2);
    
    // Check if the bottom of the sentence would be cut off
    const sentenceBottom = sentenceTop + sentenceHeight;
    const containerBottom = scrollPosition + containerHeight;
    
    // If sentence bottom would be cut off, adjust to show the entire sentence
    if (sentenceBottom > containerBottom) {
      scrollPosition = sentenceBottom - containerHeight;
    }
  }
  
  // Ensure scroll position is within bounds
  const maxScroll = storyArea.scrollHeight - containerHeight;
  const boundedScroll = Math.max(0, Math.min(scrollPosition, maxScroll));
  
  if (smooth) {
    storyArea.scrollTo({
      top: boundedScroll,
      behavior: 'smooth'
    });
  } else {
    storyArea.scrollTop = boundedScroll;
  }
}

/* Handle blank tapping in hint mode */
function handleHintBlankTap(sidx, widx) {
  const word = storyData[sidx][widx];
  if (!word.marked) return;
  
  // Toggle temporary reveal state for hint mode (same as walking mode)
  if (tempRevealState.currentRevealedWord && 
      tempRevealState.currentRevealedWord.sentenceIndex === sidx && 
      tempRevealState.currentRevealedWord.wordIndex === widx) {
    tempRevealState.lastRevealedWord = {...tempRevealState.currentRevealedWord};
    tempRevealState.currentRevealedWord = null;
  } else {
    if (tempRevealState.currentRevealedWord) {
      tempRevealState.lastRevealedWord = {...tempRevealState.currentRevealedWord};
    }
    tempRevealState.currentRevealedWord = {sentenceIndex: sidx, wordIndex: widx};
  }
  
  render();
}

/* ---------------- Rendering & interactions ---------------- */
function render(){
  const story = $('story'); story.innerHTML = '';
  
  // Show/hide mode-specific panels
  $('hintModePanel').style.display = mode === 'hint' ? 'block' : 'none';
  $('exportPanel').style.display = mode === 'quiz' ? 'block' : 'none';
  $('fileHistoryPanel').style.display = 'none';
  
  if (mode === 'hint') {
    // In hint mode, render the story area with enhanced tracking features
    storyData.forEach((sent, sidx) => {
      const sDiv = document.createElement('div'); 
      sDiv.className = 'sentence' + (sidx === hintModeState.currentSentenceIndex ? ' activeSentence' : '');
      
      // Add completed sentence class if applicable
      if (hintModeState.sentenceCompletion[sidx]) {
        sDiv.classList.add('completed-sentence');
      }
      
      sDiv.dataset.sidx = sidx;
      sDiv.style.transition = 'background .18s';
      
      sent.forEach((w, widx) => {
        const sp = document.createElement('span');
        sp.className = 'word'; 
        sp.dataset.sidx = sidx; 
        sp.dataset.widx = widx;
        
        const isTempRevealed = tempRevealState.currentRevealedWord && 
                               tempRevealState.currentRevealedWord.sentenceIndex === sidx && 
                               tempRevealState.currentRevealedWord.wordIndex === widx;
        
        // Check if this is the active blank
        const blankIndex = getBlankIndex(sidx, widx);
        // FIXED: Use currentBlankIndex as single source of truth
        const isActiveBlank = (sidx === hintModeState.currentSentenceIndex) && 
                              (blankIndex === hintModeState.currentBlankIndex);
        
        if(w.marked){ 
          const wordRevealState = hintModeState.wordRevealStates[widx];
          
          if (wordRevealState && wordRevealState.revealed) {
            if (wordRevealState.correct) {
              // Show full word when correctly revealed
              sp.textContent = w.text + ' ';
              sp.classList.add('hint-correct');
            } else if (wordRevealState.incorrect) {
              // Show full word in red when incorrectly revealed
              sp.textContent = w.text + ' ';
              sp.classList.add('hint-incorrect');
            } else if (isTempRevealed) {
              // Show full word when temporarily revealed by tap
              sp.textContent = w.text + ' ';
              sp.classList.add('temp-revealed');
            } else {
              // Show initial letter followed by underscores
              const firstLetter = w.text.charAt(0);
              const underscores = '_'.repeat(w.text.length - 1);
              sp.textContent = firstLetter + underscores + ' ';
              sp.classList.add('hint-blank');
            }
          } else if (isTempRevealed) {
            // Show full word when temporarily revealed by tap
            sp.textContent = w.text + ' ';
            sp.classList.add('temp-revealed');
          } else {
            // Show initial letter followed by underscores
            const firstLetter = w.text.charAt(0);
            const underscores = '_'.repeat(w.text.length - 1);
            sp.textContent = firstLetter + underscores + ' ';
            sp.classList.add('hint-blank');
          }
          
          // Highlight active blank
          if (isActiveBlank) {
            sp.classList.add('active-hint-blank');
            // Auto-scroll to active blank
            setTimeout(() => sp.scrollIntoView({ behavior: "smooth", block: "center" }), 50);
          }
          
          // Add completed blank styling
          if (wordRevealState && wordRevealState.revealed && wordRevealState.correct) {
            sp.classList.add('completed-blank');
          }
          
          // Add statistics below the blank if available
          if (hintModeState.blankStats[sidx] && hintModeState.blankStats[sidx][blankIndex]) {
            const stats = hintModeState.blankStats[sidx][blankIndex];
            const statsDiv = document.createElement('div');
            statsDiv.className = 'blank-stats';
            statsDiv.innerHTML = `
              <span class="correct-count">${stats.correct}</span>
              <span class="incorrect-count">${stats.incorrect}</span>
            `;
            sp.appendChild(statsDiv);
          }
        } else { 
          sp.textContent = w.text + ' '; 
        }
        
        // In hint mode, blanks can be tapped to reveal words temporarily
        if (w.marked) {
          sp.onclick = (e) => { 
            e.stopPropagation(); 
            handleHintBlankTap(sidx, widx);
            // Also center the sentence when blank is tapped
            centerActiveSentence(sidx, true);
          };
        } else {
          sp.onclick = (e) => { 
            e.stopPropagation(); 
            hintModeState.currentSentenceIndex = sidx;
            initHintMode();
            render();
          };
        }
        
        sDiv.appendChild(sp);
      });
      
      // Add performance boxes after each sentence
      if (hintModeState.sentenceTiming[sidx]) {
        const timing = hintModeState.sentenceTiming[sidx];
        const boxesContainer = document.createElement('div');
        boxesContainer.className = 'performance-boxes';
        
        boxesContainer.innerHTML = `
          <div class="performance-box">
            <div class="label">Correct Time</div>
            <div class="value">${Math.round(timing.correctTime / 1000)}s</div>
          </div>
          <div class="performance-box">
            <div class="label">Incorrect Time</div>
            <div class="value">${Math.round(timing.incorrectTime / 1000)}s</div>
          </div>
          <div class="performance-box">
            <div class="label">Completions</div>
            <div class="value">${timing.completions || 0}</div>
          </div>
          <div class="performance-box">
            <div class="label">Best Time</div>
            <div class="value">${timing.minTime ? Math.round(timing.minTime / 1000) + 's' : 'N/A'}</div>
          </div>
        `;
        
        sDiv.appendChild(boxesContainer);
      }
      
      story.appendChild(sDiv);
    });
    
    return;
  }
  
  // Normal rendering for quiz and walk modes
  storyData.forEach((sent, sidx) => {
    const sDiv = document.createElement('div'); sDiv.className = 'sentence' + (sidx === currentSentenceIndex ? ' activeSentence' : ''); sDiv.dataset.sidx = sidx;
    sDiv.style.transition = 'background .18s';
    sent.forEach((w, widx) => {
      const sp = document.createElement('span');
      sp.className = 'word'; sp.dataset.sidx = sidx; sp.dataset.widx = widx;
      
      const isTempRevealed = tempRevealState.currentRevealedWord && 
                             tempRevealState.currentRevealedWord.sentenceIndex === sidx && 
                             tempRevealState.currentRevealedWord.wordIndex === widx;
      
      if(mode === 'quiz'){
        sp.textContent = w.text + ' ';
        if(w.marked) sp.classList.add('marked');
        sp.onclick = (e) => { 
          e.stopPropagation(); 
          toggleMark(sidx, widx);
        };
      } else {
        if (sidx !== currentSentenceIndex) {
          if(w.marked){ 
            sp.textContent = '____ '; 
            sp.classList.add('blank'); 
          } else { 
            sp.textContent = w.text + ' '; 
          }
          sp.onclick = (e) => { e.stopPropagation(); navigateToSentence(sidx); };
        } else {
          if(w.marked){
            if(isTempRevealed) {
              sp.textContent = w.text + ' ';
              sp.classList.add('temp-revealed');
            } else if(w.state === 'blank'){ 
              sp.textContent = '____ '; 
              sp.classList.add('blank'); 
            }
            else if(w.state && w.state.startsWith('underlined')){ 
              sp.textContent = w.text + ' '; 
              sp.classList.add(w.state === 'underlined-mid' ? 'underlined-mid' : 'underlined-end'); 
            }
          } else { 
            sp.textContent = w.text + ' '; 
          }
          
          if (w.marked && (w.state === 'blank' || isTempRevealed)) {
            sp.onclick = (e) => { 
              e.stopPropagation(); 
              handleBlankTap(sidx, widx);
            };
          } else {
            sp.onclick = (e) => { e.stopPropagation(); navigateToSentence(sidx); };
          }
        }
      }
      sDiv.appendChild(sp);
    });
    story.appendChild(sDiv);
  });

  $('modeLabel').textContent = 'Mode: ' + (mode === 'quiz' ? 'Quiz' : mode === 'walk' ? 'Walking' : 'Hint');
  $('footerMode').textContent = mode === 'quiz' ? 'Quiz' : mode === 'walk' ? 'Walking' : 'Hint';
  $('repetitionStatus').textContent = `Rep: ${currentRepetitionCount}/${totalRepetitions}`;

  // Removed auto-centering when tapping blanks to keep sentence static
  if(mode === 'walk' && $('autoJumpToggle').checked) {
    // Only center when sentence changes, not when tapping blanks
    if (tempRevealState.currentRevealedWord === null) {
      centerActiveSentence(currentSentenceIndex);
    }
  }
  
  largeStorage.saveData();
}

// Helper function to get blank index from word index
function getBlankIndex(sentenceIndex, wordIndex) {
  const sentence = storyData[sentenceIndex];
  let blankCount = 0;
  
  for (let i = 0; i < wordIndex; i++) {
    if (sentence[i].marked) {
      blankCount++;
    }
  }
  
  return blankCount;
}

function navigateToSentence(sidx){
  resetNonActiveSentencesToBlankState();
  currentSentenceIndex = sidx; 
  playPointer.sentence = sidx; 
  playPointer.word = 0; 
  currentRepetitionCount = 0; 
  resetSentenceToBlankState(sidx); 
  render(); 
  centerActiveSentence(sidx, true);
  if(isPlaying){ if(revealResolve){ revealResolve(); revealResolve = null; } runEngine(); }
}

function updateCounts(){
  const totalWords = storyData.reduce((acc, sentence) => acc + sentence.length, 0);
  const totalBlanks = storyData.reduce((acc, sentence) => acc + sentence.filter(word => word.marked).length, 0);
  $('wordCount').textContent = `Words: ${totalWords}`; $('blankCount').textContent = `Blanks: ${totalBlanks}`;
  $('repetitionStatus').textContent = `Rep: ${currentRepetitionCount}/${totalRepetitions}`;
}

function toggleMark(sidx, widx){
  const w = storyData[sidx][widx]; w.marked = !w.marked; w.state = w.marked ? 'blank' : 'unmarked'; w.spoken = false; updateCounts(); render();
}

/* ---------------- Playback engine ---------------- */
async function runEngine(){
  totalRepetitions = parseInt(localStorage.getItem('sb_repeatCount') || $('repeatInput').value || '2');
  playPointer.sentence = currentSentenceIndex; playPointer.word = 0;

  while(isPlaying){
    const sidx = playPointer.sentence; const sent = storyData[sidx]; if(!sent) break;
    currentSentenceIndex = sidx; 
    
    resetNonActiveSentencesToBlankState();
    render();
    
    if($('autoJumpToggle').checked) {
      centerActiveSentence(sidx, true);
      await wait(300);
    }
    
    if(currentRepetitionCount >= totalRepetitions){
      await readRevealedSentence(sidx);
      if(playPointer.sentence < storyData.length - 1){ 
        playPointer.sentence++; 
        playPointer.word = 0; 
        currentSentenceIndex = playPointer.sentence; 
        currentRepetitionCount = 0; 
        resetSentenceToBlankState(playPointer.sentence); 
        await wait(200); 
        continue; 
      } else { 
        isPlaying = false; 
        break; 
      }
    }

    while(playPointer.word < sent.length && isPlaying){
      const w = sent[playPointer.word];
      if(w.marked && w.state === 'blank'){
        waitingForReveal = { sidx, idx: playPointer.word }; setIndicator(true);
        await waitForReveal();
        await wait(450);
        continue;
      }
      if(w.marked && w.state && w.state.startsWith('underlined')){
        let j = playPointer.word; const group = [];
        while(j < sent.length && sent[j].marked && sent[j].state && sent[j].state.startsWith('underlined')){ group.push(j); j++; }
        const groupText = group.map(i => sent[i].text).join(' ');
        if(groupText) await speakText(groupText);
        group.forEach(i => sent[i].spoken = true);
        playPointer.word = j; continue;
      }
      if(!w.marked){
        let j = playPointer.word; const chunk = [];
        while(j < sent.length && !sent[j].marked){ chunk.push(sent[j].text); j++; }
        const phrase = chunk.join(' ');
        if(phrase) await speakText(phrase);
        const hadUnderlined = sent.some(x => x.marked && x.state && x.state.startsWith('underlined') && !x.spoken);
        if(hadUnderlined) blankAllUnderlinesInSentence(sidx);
        playPointer.word = j; continue;
      }
      playPointer.word++;
    }

    if(!isPlaying) break;
    currentRepetitionCount++; updateCounts();
    if(currentRepetitionCount < totalRepetitions){ resetSentenceToBlankState(sidx); playPointer.word = 0; await wait(250); }
  }

  waitingForReveal = null; setIndicator(false); isPlaying = false; $('playPauseBtn').textContent = '▶';
}

async function readRevealedSentence(sidx){
  const sent = storyData[sidx]; if(!sent) return;
  for(let widx = 0; widx < sent.length; widx++){ if(sent[widx].marked && sent[widx].state === 'blank'){ const g = getContiguousGroup(sidx, widx); classifyGroupAndSetStates(sidx, g); widx = g[g.length - 1]; } }
  render();
  let currentWord = 0;
  while(currentWord < sent.length && isPlaying){
    const w = sent[currentWord];
    if(w.marked && w.state && w.state.startsWith('underlined')){
      let j = currentWord; const group = [];
      while(j < sent.length && sent[j].marked && sent[j].state && sent[j].state.startsWith('underlined')){ group.push(j); j++; }
      const groupText = group.map(i => sent[i].text).join(' ');
      if(groupText) await speakText(groupText);
      currentWord = j;
    } else {
      let j = currentWord; const chunk = [];
      while(j < sent.length && !sent[j].marked){ chunk.push(sent[j].text); j++; }
      const phrase = chunk.join(' ');
      if(phrase) await speakText(phrase);
      currentWord = j;
    }
  }
}

function blankAllUnderlinesInSentence(sidx){
  const sent = storyData[sidx]; if(!sent) return;
  for(let i=0;i<sent.length;i++){ if(sent[i].marked && sent[i].state && sent[i].state.startsWith('underlined') && !sent[i].spoken){ sent[i].state = 'blank'; } }
  render();
}

function startPlayback(){
  if(storyData.length === 0){ notify('No story loaded'); return; }
  if(mode !== 'walk'){ notify('Switch to Walking mode to play'); return; }
  if(isPlaying) return;
  isPlaying = true; $('playPauseBtn').textContent = '⏸'; runEngine();
}

function stopPlayback(){
  if(!isPlaying) return;
  isPlaying = false;
  if(revealResolve){ revealResolve(); revealResolve = null; }
  setIndicator(false); $('playPauseBtn').textContent = '▶';
  try{ window.speechSynthesis.cancel(); }catch(e){}
}

function togglePlayPause(){ if(isPlaying) stopPlayback(); else startPlayback(); }

function goPrev(){ 
  if(currentSentenceIndex > 0){ 
    resetNonActiveSentencesToBlankState();
    currentSentenceIndex--; 
    playPointer.sentence = currentSentenceIndex; 
    playPointer.word = 0; 
    currentRepetitionCount = 0; 
    resetSentenceToBlankState(currentSentenceIndex); 
    render(); 
    centerActiveSentence(currentSentenceIndex, true); 
  } 
}

function goNext(){ 
  if(currentSentenceIndex < storyData.length - 1){ 
    resetNonActiveSentencesToBlankState();
    currentSentenceIndex++; 
    playPointer.sentence = currentSentenceIndex; 
    playPointer.word = 0; 
    currentRepetitionCount = 0; 
    resetSentenceToBlankState(currentSentenceIndex); 
    render(); 
    centerActiveSentence(currentSentenceIndex, true); 
  } 
}

function handleFileInput(file){
  if(!file) return;
  const reader = new FileReader();
  reader.onload = (e) => {
    const text = e.target.result;
    if(file.name.toLowerCase().endsWith('.json')){
      try{
        const obj = JSON.parse(text);
        if(Array.isArray(obj)){ storyData = obj; currentSentenceIndex = 0; currentRepetitionCount = 0; updateCounts(); render(); notify('Imported JSON story'); return; }
        else if(obj.storyData){ storyData = obj.storyData; currentSentenceIndex = 0; currentRepetitionCount = 0; updateCounts(); render(); notify('Imported JSON story'); return; }
      }catch(err){ notify('Invalid JSON file'); return; }
    }
    parseAndLoadText(String(text));
    addToFileHistory(file.name, text);
    notify('Loaded text file');
  };
  reader.readAsText(file);
}

function getStoryExportJSON(){ return JSON.stringify(storyData, null, 2); }
function getBlanksExportText(){ return storyData.map(sent => sent.map(w => (w.marked && w.state === 'blank') ? '____' : w.text).join(' ')).join('\n\n'); }
function getAnswersExportText(){ return storyData.map(sent => sent.map(w => w.text).join(' ')).join('\n\n'); }
async function copyToClipboard(str){ try{ await navigator.clipboard.writeText(str); notify('Copied to clipboard'); }catch(e){ notify('Clipboard not available'); } }
function shareViaWhatsApp(text){ const url = 'https://wa.me/?text=' + encodeURIComponent(text); window.open(url, '_blank'); }

// NEW: Export analytics function
function exportAnalytics() {
  const analytics = {
    totalHintTime: hintModeState.totalHintTime,
    sentenceCompletion: hintModeState.sentenceCompletion,
    blankStats: hintModeState.blankStats,
    sentenceTiming: hintModeState.sentenceTiming,
    exportDate: new Date().toISOString()
  };
  
  const blob = new Blob([JSON.stringify(analytics, null, 2)], {type: 'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `storyblank-analytics-${Date.now()}.json`;
  a.click();
  notify('Analytics exported');
}

function importStoryFile(file){
  if(!file) return;
  const reader = new FileReader();
  reader.onload = (e) => {
    const text = e.target.result;
    try {
      if(text.startsWith('{') || text.startsWith('[')){
        const obj = JSON.parse(text);
        if(Array.isArray(obj)){ storyData = obj; currentSentenceIndex = 0; currentRepetitionCount = 0; updateCounts(); render(); notify('Imported story JSON'); return; }
        if(obj.storyData && Array.isArray(obj.storyData)){ storyData = obj.storyData; currentSentenceIndex = 0; currentRepetitionCount = 0; updateCounts(); render(); notify('Imported story JSON'); return; }
      }
    } catch(err){}
    parseAndLoadText(String(text));
    notify('Imported plain text story');
  };
  reader.readAsText(file);
}

function testVoice() {
  const status = $('voiceStatus');
  status.textContent = "Testing voice...";
  
  if (!('speechSynthesis' in window)) {
  status.textContent = "Speech synthesis not supported in your browser";
  return;
  }
  
  speakText("This is a voice test. If you can hear this, text to speech is working.")
    .then(() => {
      status.textContent = "Voice test completed successfully";
    })
    .catch((e) => {
      status.textContent = "Voice test failed: " + e.message;
    });
}

/* ENHANCED: Improved tap detection for walking mode single-blank revelation */
function setupDropdowns() {
  document.addEventListener('click', function(e) {
    const gearPanel = $('gearPanel');
    const gearBtn = $('gearBtn');
    if (gearPanel && gearPanel.style.display !== 'none' && 
        !gearPanel.contains(e.target) && e.target !== gearBtn) {
      gearPanel.style.display = 'none';
    }
    
    const fileHistoryPanel = $('fileHistoryPanel');
    const historyBtn = $('showHistoryBtn');
    if (fileHistoryPanel && fileHistoryPanel.style.display !== 'none' && 
        !fileHistoryPanel.contains(e.target) && e.target !== historyBtn) {
      fileHistoryPanel.style.display = 'none';
    }
    
    // Enhanced: Detect taps outside story area for walking mode single-blank revelation
    const storyArea = $('storyArea');
    const storyContainer = $('story-container');
    
    // Check if click is outside the story content area
    const clickedInsideStory = storyArea.contains(e.target) || 
                             storyContainer.contains(e.target) ||
                             e.target.closest('.word, .sentence, #story');
    
    // In walking mode, if waiting for reveal and user taps outside story area
    if (mode === 'walk' && waitingForReveal && !clickedInsideStory) {
      notifyRevealFromUI();
    }
    
    if ((mode === 'walk' || mode === 'hint') && tempRevealState.currentRevealedWord) {
      const clickedWord = e.target.closest('.word');
      if (!clickedWord || !clickedWord.classList.contains('blank')) {
        resetAllTempReveals();
      }
    }
  });
}

/* ---------------- Event wiring & init ---------------- */
function initEventListeners(){
  $('loadBtn').onclick = () => { $('fileInput').click(); };
  $('fileInput').onchange = (e) => { 
    const f = e.target.files[0]; 
    if(f){ 
      handleFileInput(f); 
      e.target.value = ''; 
    } 
  };

  $('loadFromTextBtn').onclick = () => { 
    const t = $('storyInput').value.trim(); 
    if(!t){ notify('Paste text first'); return; } 
    parseAndLoadText(t); 
    addToFileHistory('Text Input', t);
    notify('Loaded from text area'); 
  };
  
  $('startReadBtn').onclick = () => { if(mode !== 'walk'){ notify('Switch to Walking mode to play'); return; } startPlayback(); };
  $('stopReadBtn').onclick = () => { stopPlayback(); notify('Stopped'); };

  $('switchToWalkBtn').onclick = () => { 
    mode = 'walk'; 
    storyData.forEach((_,sidx) => resetSentenceToBlankState(sidx)); 
    currentRepetitionCount = 0; 
    render(); 
    notify('Mode: Walking'); 
  };
  
  $('switchToQuizBtn').onclick = () => { mode = 'quiz'; render(); notify('Mode: Quiz'); };
  
  $('switchToHintBtn').onclick = () => { 
    mode = 'hint'; 
    if (hintModeState.currentSentenceIndex < 0 || hintModeState.currentSentenceIndex >= storyData.length) {
      hintModeState.currentSentenceIndex = 0;
    }
    initHintMode();
    render(); 
    notify('Mode: Hint'); 
  };
  
  $('toggleModeBtn').onclick = () => { 
    if (mode === 'quiz') {
      mode = 'walk';
      storyData.forEach((_,sidx) => resetSentenceToBlankState(sidx));
    } else if (mode === 'walk') {
      mode = 'hint';
      if (hintModeState.currentSentenceIndex < 0 || hintModeState.currentSentenceIndex >= storyData.length) {
        hintModeState.currentSentenceIndex = 0;
      }
      initHintMode();
    } else {
      mode = 'quiz';
    }
    render(); 
    notify('Mode: ' + (mode === 'quiz' ? 'Quiz' : mode === 'walk' ? 'Walking' : 'Hint')); 
  };

  $('showHistoryBtn').onclick = () => { 
    $('fileHistoryPanel').style.display = $('fileHistoryPanel').style.display === 'none' ? 'block' : 'none'; 
    renderFileHistory();
  };
  $('clearHistoryBtn').onclick = clearFileHistory;

  $('gearBtn').onclick = () => { $('gearPanel').style.display = $('gearPanel').style.display === 'none' ? 'block' : 'none'; };
  $('helpBtn').onclick = () => toggleHelp();
  $('clearBtn').onclick = () => { 
    storyData = []; 
    currentSentenceIndex = 0; 
    currentRepetitionCount = 0; 
    hintModeState.sentenceStats = [];
    hintModeState.blankStats = {};
    hintModeState.sentenceCompletion = {};
    hintModeState.sentenceTiming = {};
    hintModeState.selectedLetters = [];
    tempRevealState = { currentRevealedWord: null, lastRevealedWord: null };
    render(); 
    notify('Cleared story'); 
  };
  $('testVoiceBtn').onclick = testVoice;

  $('repeatInput').onchange = (e) => { localStorage.setItem('sb_repeatCount', String(e.target.value)); totalRepetitions = parseInt(e.target.value || '2'); render(); };
  $('soundToggle').onchange = (e) => { localStorage.setItem('sb_soundToggle', e.target.checked ? '1' : '0'); };
  $('autoJumpToggle').onchange = (e) => { localStorage.setItem('sb_autoJumpToggle', e.target.checked ? '1' : '0'); };
  $('voiceSpeedInput').onchange = (e) => { localStorage.setItem('sb_voiceSpeed', String(e.target.value)); };

  $('playPauseBtn').onclick = togglePlayPause;
  $('prevBtn').onclick = goPrev;
  $('nextBtn').onclick = goNext;
  $('voicableSmallBtn').onclick = () => notifyRevealFromUI();
  $('revealBtn').onclick = () => { notifyRevealFromUI(); if($('soundToggle').checked){ const d = $('ding'); try{ d.currentTime=0; d.play().catch(()=>{}); }catch(e){} } };

  $('hintCheckBtn').onclick = () => { /* Check functionality is now integrated in selectHintLetter */ };
  $('hintResetBtn').onclick = resetHintSentence;
  $('hintNextBtn').onclick = nextHintSentence;
  $('hintPrevBtn').onclick = prevHintSentence;
  $('showAnalysisBtn').onclick = showHintAnalysis;
  $('closeAnalysisBtn').onclick = () => { $('hintAnalysisPanel').style.display = 'none'; };

  $('exportBlanksBtn').onclick = () => { const text = getBlanksExportText(); const blob = new Blob([text], {type:'text/plain'}); const a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'story-blanks.txt'; a.click(); notify('Exported blanks'); };
  $('exportAnswersBtn').onclick = () => { const text = getAnswersExportText(); const blob = new Blob([text], {type:'text/plain'}); const a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'story-answers.txt'; a.click(); notify('Exported answers'); };
  $('copyToClipboardBtn').onclick = () => copyToClipboard(getBlanksExportText());
  $('shareWhatsAppBtn').onclick = () => shareViaWhatsApp(getBlanksExportText());
  $('exportAnalyticsBtn').onclick = exportAnalytics;

  // Enhanced global click handler for walking mode single-blank revelation
  document.addEventListener('click', (e) => {
    const storyArea = $('storyArea');
    const storyContainer = $('story-container');
    
    // Check if click is outside the story content area
    const clickedInsideStory = storyArea.contains(e.target) || 
                             storyContainer.contains(e.target) ||
                             e.target.closest('.word, .sentence, #story');
    
    if(waitingForReveal && !clickedInsideStory){ 
      notifyRevealFromUI(); 
    }
  });

  document.addEventListener('keydown', (e) => {
    if((e.ctrlKey || e.metaKey) && e.key === ' '){ e.preventDefault(); togglePlayPause(); }
    if(e.key === 'ArrowRight') goNext();
    if(e.key === 'ArrowLeft') goPrev();
    if(e.code === 'Space' && isPlaying){ e.preventDefault(); notifyRevealFromUI(); }
  });
  
  setupDropdowns();
}

let helpOpen = false;
function toggleHelp(){
  if(!helpOpen){
    const p = document.createElement('div'); p.id = 'helpOverlay';
    p.style.cssText = 'position:fixed;top:60px;left:15px;right:15px;max-width:800px;margin:auto;background:#071025;border:1px solid rgba(255,255,255,0.04);padding:10px;border-radius:6px;z-index:200; font-size: 14px;';
    p.innerHTML = `<div class="help"><strong>Instructions</strong><ul>
      <li><strong>Quiz Mode:</strong> Mark words by tapping to create blanks for testing.</li>
      <li><strong>Walking/Read-Aloud Mode:</strong> Sentences jump to the center when being voiced. Reader PAUSES at blanks. Tap outside story area or press Reveal to reveal next blank.</li>
      <li><strong>Hint Mode:</strong> Practice sentences by arranging initial letters in correct order. Track your progress with detailed analytics.</li>
      <li><strong>Blank Tapping:</strong> In both Walking and Hint modes, tap blanks to temporarily reveal words. Tap outside to hide all revealed words.</li>
      <li><strong>Letter Selection:</strong> Only the tapped letter changes color. Tap the same letter again to deselect it.</li>
      <li><strong>Pink Asterisk:</strong> Shows the connection between tapped letter and corresponding blank in the sentence. The asterisk appears on the same blank in both the story area and hint panel.</li>
      <li>Non-active sentences always show blanks for marked words in walking mode.</li>
      <li>Export/Import: use Export Story with Marks to save storyData and Import to restore exact marks.</li>
      <li>Spacebar during play will reveal the next blank.</li>
      <li>Tap any word to navigate to that sentence.</li>
      <li>Use the "Test Voice" button to check if text-to-speech is working.</li>
    </ul></div>`;
    document.body.appendChild(p); helpOpen = true;
    setTimeout(()=>{ const p2 = $('helpOverlay'); if(p2) p2.remove(); helpOpen=false; }, 12000);
  } else { const p = $('helpOverlay'); if(p) p.remove(); helpOpen=false; }
}

document.addEventListener('DOMContentLoaded', function() {
    $('repeatInput').value = localStorage.getItem('sb_repeatCount') || $('repeatInput').value;
    $('soundToggle').checked = localStorage.getItem('sb_soundToggle') !== '0';
    $('autoJumpToggle').checked = localStorage.getItem('sb_autoJumpToggle') !== '0';
    $('voiceSpeedInput').value = localStorage.getItem('sb_voiceSpeed') || $('voiceSpeedInput').value;
    totalRepetitions = parseInt($('repeatInput').value);
    
    if(!$('storyInput').value) $('storyInput').value = 'The Vijayanagara Empire was renowned for its glory, wealth, and cultural achievements.';
    parseAndLoadText($('storyInput').value);
    
    if (fileHistory.currentPage) {
        mode = fileHistory.currentPage.mode || 'quiz';
        if (mode === 'hint') {
            hintModeState.currentSentenceIndex = fileHistory.currentPage.sentenceIndex || 0;
        } else {
            currentSentenceIndex = fileHistory.currentPage.sentenceIndex || 0;
        }
        render();
    }
    
    initEventListeners();
});
</script>

</body>
</html>
