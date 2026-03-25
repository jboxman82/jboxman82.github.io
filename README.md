<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=1100"> 
    <title>Pro Baseball Lineup Manager</title>
    <style>
        :root {
            --bg: #f0f2f5;
            --card: #ffffff;
            --primary: #1a202c;
            --success: #38a169;
            --danger: #e53e3e;
            --info: #3182ce;
            --border: #d1d5db;
            --header-bg: #e2e8f0; /* Light Gray Variable */
        }

        body { 
            font-family: 'Segoe UI', system-ui, sans-serif; 
            background: var(--bg); margin: 0; padding: 10px;
            min-width: 1100px; 
        }
        
        .uniform-card {
            background: var(--card); border-radius: 8px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
            padding: 10px; text-align: center;
            display: flex; flex-direction: column;
            border: 1px solid var(--border);
            position: relative;
            min-height: 400px; 
        }

        /* Updated Header Color to Light Gray */
        .setup-header-static {
            background: var(--header-bg); color: var(--primary); padding: 8px;
            border-radius: 4px; margin-bottom: 5px;
            display: flex; flex-direction: column; align-items: center;
            font-size: 13px; font-weight: bold;
            border: 1px solid var(--border);
        }

        .branding-row {
            display: flex; gap: 10px; width: 100%; margin-top: 5px;
            font-size: 10px; font-weight: normal;
        }
        .brand-input {
            background: transparent; border: none; border-bottom: 1px solid #cbd5e0;
            color: var(--primary); font-size: 11px; padding: 0 5px; flex-grow: 1;
        }
        .brand-input::placeholder { color: #718096; }

        .roster-header {
            display: flex; gap: 3px; font-weight: bold; font-size: 11px;
            color: #4a5568; margin-bottom: 5px; border-bottom: 1px solid #edf2f7;
            align-items: center; text-align: center;
        }

        /* Drag and Drop Units */
        .roster-entry { 
            display: flex; gap: 3px; margin-bottom: 1px; align-items: center; padding: 2px 0; 
            cursor: grab; transition: background 0.2s;
        }
        .roster-entry:active { cursor: grabbing; }
        .roster-entry.dragging { opacity: 0.5; background: #ebf8ff; border: 1px dashed var(--info); }
        .roster-entry:nth-child(even) { background-color: #f9fafb; }
        
        .col-idx { width: 22px; text-align: center; font-weight: bold; color: #718096; font-size: 11px; pointer-events: none; }
        .col-name { width: 180px; text-align: center; }
        .col-num { width: 45px; text-align: center; }
        .col-inn { width: 45px; text-align: center; }

        input.p-name { width: 170px; text-align: left; font-weight: 500; }
        input.p-num { width: 35px; text-align: center; font-weight: bold; }
        
        .p-stat { font-size: 12px; font-weight: bold; color: #1a202c; }

        #main-grid {
            display: grid; grid-template-columns: repeat(3, 350px); 
            gap: 12px; margin-bottom: 12px;
        }

        .top-controls {
            grid-column: span 3;
            display: flex; justify-content: space-between; align-items: center;
            margin-bottom: 8px; padding: 0 5px;
        }

        #safety-card-wide {
            grid-column: span 3; padding: 12px; background: #f8fafc;
            text-align: left; min-height: auto;
        }
        #safety-card-wide h5 { margin: 0; text-transform: uppercase; font-size: 12px; color: #1a202c; border-bottom: 2px solid var(--primary); display: inline-block; padding-right: 20px; }
        
        .safety-flex { display: flex; justify-content: space-between; gap: 40px; margin-top: 8px; }
        .rest-table { flex: 1; display: grid; grid-template-columns: repeat(5, 1fr); gap: 10px; }
        .rest-box { border: 1px solid #cbd5e0; padding: 5px; border-radius: 4px; text-align: center; background: white; }
        .rest-box strong { display: block; font-size: 11px; color: var(--primary); }
        .rest-box span { font-size: 10px; }

        .safety-text { flex: 1; font-size: 11px; line-height: 1.4; border-left: 2px solid var(--border); padding-left: 20px; }

        .field-container { 
            width: 330px; height: 320px; 
            margin: 0 auto; position: relative; 
            display: flex; align-items: center; justify-content: center;
        }
        
        select.pos {
            position: absolute; width: 85px; font-size: 10px; z-index: 10; 
            transform: translate(-50%, -50%); border: 1.5px solid #000;
            background: white; border-radius: 2px;
            text-align: center; text-align-last: center;
        }
        
        .P { top: 60%; left: 50%; } .C { top: 92%; left: 50%; }
        .FB { top: 52%; left: 85%; } .SB { top: 32%; left: 70%; }
        .SS { top: 32%; left: 30%; } .TB { top: 52%; left: 15%; }
        .LF { top: 12%; left: 15%; } .CF { top: 5%; left: 50%; }
        .RF { top: 12%; left: 85%; }

        .bench-list {
            margin-top: auto; font-size: 10px; text-align: left;
            color: #4a5568; padding-top: 5px; border-top: 1px dashed #ccc;
            min-height: 25px;
        }

        .inning-header-row {
            display: flex; justify-content: space-between; align-items: center;
            margin-bottom: 5px; gap: 5px;
        }

        .copy-inning-controls { display: flex; gap: 3px; align-items: center; }
        .copy-select { font-size: 10px; padding: 2px; border-radius: 3px; border: 1px solid #ccc; }
        .btn-copy-action { background: var(--info); color: white; padding: 2px 6px; font-size: 9px; border-radius: 3px; border: none; cursor: pointer; font-weight: bold; }

        .print-score-container { display: none; }

        button { padding: 6px 12px; border-radius: 4px; border: none; font-weight: 600; cursor: pointer; font-size: 12px; height: 32px; display: flex; align-items: center; justify-content: center; }
        .btn-update { background: var(--success); color: white; width: 100%; margin-top: 5px; }
        .btn-print-top { background: #000; color: white; flex-grow: 0; min-width: 120px; }
        .btn-clear-innings { background: var(--danger); color: white; flex-grow: 0; min-width: 120px; }
        .btn-clear-single { background: var(--danger); color: white; font-size: 10px; padding: 4px 8px; height: auto; }
        .btn-clear-roster { background: #718096; color: white; width: 100%; margin-top: 5px; height: auto; padding: 6px; }

        @media print {
            @page { size: portrait; margin: 0.1in; }
            body { min-width: auto; padding: 0; background: white; -webkit-print-color-adjust: exact; }
            
            #main-grid { grid-template-columns: repeat(3, 1fr); gap: 5px; transform: scale(0.97); transform-origin: top center; }
            .uniform-card { box-shadow: none; border: 1px solid #000; height: 355px !important; padding: 4px; }
            .btn-update, #load-file-label, #save-btn, .copy-inning-controls, .btn-clear-single, .btn-clear-roster, .top-controls { display: none !important; }

            .print-score-container { display: flex; gap: 10px; font-size: 9pt; font-weight: bold; }
            .score-underline { border-bottom: 1px solid black; min-width: 40px; display: inline-block; margin-left: 2px; }

            /* Ensure header remains gray in print if needed, or white for ink saving */
            .setup-header-static { background: #f1f5f9 !important; color: black !important; border: 1px solid black !important; }
            .brand-input { border-bottom: 1px solid black; color: black !important; font-weight: bold; }

            #safety-card-wide { grid-column: span 3; height: auto !important; margin-top: -5px; background: white !important; border: 1px solid #000; }
            select.pos { appearance: none; -webkit-appearance: none; border: 1px solid #666; text-align: center; text-align-last: center; }

            .roster-entry { cursor: default; }
            .roster-entry input { border: none; font-size: 9pt; background: transparent; padding: 0; }
            .roster-entry:nth-child(even) { background-color: #f3f4f6 !important; }
            
            .field-container { transform: scale(0.95); transform-origin: center top; margin-top: 5px; height: 280px; }
            h4 { margin: 0; font-size: 11pt; }
        }
    </style>
</head>
<body>

<div id="main-grid">
    <div class="top-controls">
        <div style="width: 120px;"></div> 
        <button class="btn-print-top" onclick="window.print()">Print Lineup</button>
        <button class="btn-clear-innings" onclick="clearAllInnings()">Clear All Innings</button>
    </div>

    <div class="uniform-card" id="roster-card">
        <div class="setup-header-static">
            <div>BATTING ORDER</div>
            <div class="branding-row">
                Team: <input type="text" class="brand-input" placeholder="Team Name" id="team-name">
                Date: <input type="text" class="brand-input" style="width: 80px;" placeholder="mm/dd/yy" id="game-date">
            </div>
        </div>
        <div id="setup-content">
            <div class="roster-header">
                <span class="col-idx"></span>
                <span class="col-name">Name</span>
                <span class="col-num">#</span>
                <span class="col-inn">Inn</span>
            </div>
            <div id="roster-list"></div>
            <button class="btn-clear-roster" onclick="clearPlayerNames()">Clear Player Names</button>
            <button class="btn-update" onclick="updateRoster()">Finalize Roster</button>
            <div style="display:flex; gap:3px; margin-top:3px;">
                <button id="save-btn" style="background:var(--info); color:white; flex:1; height: auto; padding: 8px;" onclick="saveGame()">Save</button>
                <label id="load-file-label" style="background:#718096; flex:1; cursor:pointer; padding: 8px; border-radius: 4px; color:white; font-weight:600; font-size:11px; text-align:center; height: auto; display: flex; align-items: center; justify-content: center;">
                    Load <input type="file" id="load-file" style="display:none" onchange="loadGame(event)">
                </label>
            </div>
        </div>
    </div>

    <div class="uniform-card" id="safety-card-wide">
        <h5>Pitching & Catcher Safety Rules</h5>
        <div class="safety-flex">
            <div class="rest-table">
                <div class="rest-box"><strong>1–20</strong><span>0 Days Rest</span></div>
                <div class="rest-box"><strong>21–35</strong><span>1 Day Rest</span></div>
                <div class="rest-box"><strong>36–50</strong><span>2 Days Rest</span></div>
                <div class="rest-box"><strong>51–65</strong><span>3 Days Rest</span></div>
                <div class="rest-box"><strong>66+</strong><span>4 Days Rest</span></div>
            </div>
            <div class="safety-text">
                <strong>Pitchers to Catchers:</strong> A player who throws 41 or more pitches in a game cannot play catcher for the remainder of that day.<br>
                <strong>Catchers to Pitchers:</strong> A player who has caught 4 or more innings in a game cannot pitch that day.
            </div>
        </div>
    </div>
</div>

<script>
    const posKeys = ['P','C','1st','2nd','SS','3rd','LF','CF','RF'];
    let players = [];

    function addRow(name='', num='') {
        const list = document.getElementById('roster-list');
        const count = list.children.length + 1;
        if (count > 13) return; 
        const div = document.createElement('div');
        div.className = 'roster-entry';
        div.draggable = true;
        div.innerHTML = `
            <span class="col-idx">${count}.</span> 
            <div class="col-name"><input type="text" class="p-name" value="${name}"></div>
            <div class="col-num"><input type="text" class="p-num" value="${num}"></div>
            <div class="col-inn"><span class="p-stat" id="stat-${count}">0</span></div>
        `;
        div.addEventListener('dragstart', () => div.classList.add('dragging'));
        div.addEventListener('dragend', () => { div.classList.remove('dragging'); reindexRoster(); updateRoster(); });
        list.appendChild(div);
    }

    const rosterList = document.getElementById('roster-list');
    rosterList.addEventListener('dragover', e => {
        e.preventDefault();
        const draggingItem = document.querySelector('.dragging');
        const afterElement = getDragAfterElement(rosterList, e.clientY);
        if (afterElement == null) rosterList.appendChild(draggingItem);
        else rosterList.insertBefore(draggingItem, afterElement);
    });

    function getDragAfterElement(container, y) {
        const draggableElements = [...container.querySelectorAll('.roster-entry:not(.dragging)')];
        return draggableElements.reduce((closest, child) => {
            const box = child.getBoundingClientRect();
            const offset = y - box.top - box.height / 2;
            if (offset < 0 && offset > closest.offset) return { offset: offset, element: child };
            else return closest;
        }, { offset: Number.NEGATIVE_INFINITY }).element;
    }

    function reindexRoster() {
        document.querySelectorAll('.roster-entry').forEach((entry, i) => {
            entry.querySelector('.col-idx').innerText = `${i + 1}.`;
            const statSpan = entry.querySelector('.p-stat');
            if(statSpan) statSpan.id = `stat-${i + 1}`;
        });
    }

    function createFields() {
        const grid = document.getElementById('main-grid');
        const footer = document.getElementById('safety-card-wide');
        for (let i = 1; i <= 7; i++) {
            const card = document.createElement('div');
            card.className = 'uniform-card inning-card';
            let copyOptions = '';
            for(let j=1; j<=7; j++) { if(i !== j) copyOptions += `<option value="${j}">Inn ${j}</option>`; }

            card.innerHTML = `
                <div class="inning-header-row">
                    <h4 style="margin:0">Inn ${i}</h4>
                    <div class="copy-inning-controls">
                        <select id="copy-src-${i}" class="copy-select">${copyOptions}</select>
                        <button class="btn-copy-action" onclick="copySpecificInning(${i})">Copy</button>
                    </div>
                    <div class="print-score-container">
                        <span>H:<span class="score-underline"></span></span>
                        <span>A:<span class="score-underline"></span></span>
                    </div>
                    <button class="btn-clear-single" onclick="clearSingleInning(${i})">Clear</button>
                </div>
            `;
            const field = document.createElement('div');
            field.className = 'field-container';
            field.innerHTML = `
                <svg viewBox="0 0 200 200" style="width:100%; height:100%">
                    <line x1="100" y1="190" x2="5" y2="95" stroke="black" stroke-width="2"/>
                    <line x1="100" y1="190" x2="195" y2="95" stroke="black" stroke-width="2"/>
                    <path d="M 15,105 A 110,110 0 0 1 185,105" fill="none" stroke="black" stroke-width="1.5" stroke-dasharray="3"/>
                    <rect x="71" y="111" width="58" height="58" fill="none" stroke="black" stroke-width="2" transform="rotate(45 100 140)"/>
                    <circle cx="100" cy="125" r="8" fill="none" stroke="black" stroke-width="1.5"/>
                    <path d="M 100,192 L 94,184 L 94,176 L 106,176 L 106,184 Z" fill="black" />
                </svg>`;

            posKeys.forEach(p => {
                const sel = document.createElement('select');
                let classP = p === '1st' ? 'FB' : (p === '2nd' ? 'SB' : (p === '3rd' ? 'TB' : p));
                sel.className = `pos ${classP}`;
                sel.dataset.inning = i; sel.dataset.pos = p;
                sel.onchange = () => syncInning(i);
                sel.innerHTML = `<option value="">-- ${p} --</option>`;
                field.appendChild(sel);
            });
            card.appendChild(field);
            const bench = document.createElement('div');
            bench.className = 'bench-list'; bench.id = `bench-${i}`; bench.innerHTML = 'Bench: ';
            card.appendChild(bench);
            grid.insertBefore(card, footer);
        }
    }

    function copySpecificInning(targetInning) {
        const srcInning = document.getElementById(`copy-src-${targetInning}`).value;
        const srcSelects = document.querySelectorAll(`select[data-inning="${srcInning}"]`);
        const targetSelects = document.querySelectorAll(`select[data-inning="${targetInning}"]`);
        srcSelects.forEach((src, idx) => { targetSelects[idx].value = src.value; });
        syncInning(targetInning);
    }

    function clearPlayerNames() {
        if(!confirm("Clear all player names and numbers?")) return;
        document.querySelectorAll('.p-name').forEach(el => el.value = '');
        document.querySelectorAll('.p-num').forEach(el => el.value = '');
        updateRoster();
    }

    function clearAllInnings() {
        if(!confirm("Clear all assignments for all 7 innings?")) return;
        document.querySelectorAll('select.pos').forEach(el => el.value = '');
        for(let i=1; i<=7; i++) syncInning(i);
    }

    function clearSingleInning(inningNum) {
        document.querySelectorAll(`select[data-inning="${inningNum}"]`).forEach(el => el.value = '');
        syncInning(inningNum);
    }

    function updateRoster() {
        const names = document.querySelectorAll('.p-name');
        const nums = document.querySelectorAll('.p-num');
        players = [];
        names.forEach((n, i) => { if(n.value) players.push({ name: n.value, num: nums[i].value, idx: (i + 1) }); });
        for (let i = 1; i <= 7; i++) syncInning(i);
    }

    function syncInning(inningNum) {
        const selects = document.querySelectorAll(`select[data-inning="${inningNum}"]`);
        const used = Array.from(selects).map(s => s.value).filter(v => v !== "");
        selects.forEach(select => {
            const current = select.value;
            let html = `<option value="">-- ${select.dataset.pos} --</option>`;
            players.forEach(p => { if (!(used.includes(p.name) && p.name !== current)) { html += `<option value="${p.name}" ${p.name === current ? 'selected' : ''}>#${p.num} ${p.name}</option>`; } });
            select.innerHTML = html;
        });
        const benchEl = document.getElementById(`bench-${inningNum}`);
        const benched = players.filter(p => !used.includes(p.name)).map(p => p.name);
        benchEl.innerHTML = `<strong>Bench:</strong> ${benched.join(', ')}`;
        updateStats();
    }

    function updateStats() {
        players.forEach(p => {
            let count = 0;
            document.querySelectorAll('select.pos').forEach(s => { if(s.value === p.name) count++; });
            const statEl = document.getElementById(`stat-${p.idx}`);
            if (statEl) statEl.innerText = count;
        });
    }

    function saveGame() {
        const data = { team: document.getElementById('team-name').value, date: document.getElementById('game-date').value, players: players, lineup: Array.from(document.querySelectorAll('select.pos')).map(s => ({ inning: s.dataset.inning, pos: s.dataset.pos, val: s.value })) };
        const blob = new Blob([JSON.stringify(data)], {type: 'application/json'});
        const a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'game-day-lineup.json'; a.click();
    }

    function loadGame(event) {
        const file = event.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = (e) => {
  
