import streamlit as st
import streamlit.components.v1 as components

st.set_page_config(
    page_title="공식 렌주룰 오목 (Omok)",
    page_icon="⚫",
    layout="wide"
)

st.title("⚫⚪ 공식 렌주룰 오목 (15×15 표준 규칙)")
st.markdown("""
- **공식 규칙 적용:** 흑돌 선공 / **흑돌 3-3 · 4-4 · 장목(6목 이상) 금수** / 백돌 금수 없음 및 6목 승리 인정
- **금수 안내:** 흑돌 차례에 착수할 수 없는 금수 자리는 보드 위에 붉은색 **`✕`**로 표시됩니다.
""")

omok_html = """
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<style>
    body {
        margin: 0;
        padding: 10px;
        background-color: #0f172a;
        color: #f8fafc;
        font-family: 'Pretendard', 'Malgun Gothic', sans-serif;
        display: flex;
        justify-content: center;
        align-items: flex-start;
        gap: 24px;
        user-select: none;
    }
    .board-wrapper {
        position: relative;
        padding: 14px;
        background: linear-gradient(145deg, #8b5a2b, #5c3a18);
        border-radius: 12px;
        box-shadow: 0 20px 45px rgba(0, 0, 0, 0.75), inset 0 1px 2px rgba(255,255,255,0.25);
    }
    canvas {
        display: block;
        border-radius: 4px;
        cursor: pointer;
        box-shadow: inset 0 0 12px rgba(0,0,0,0.35);
    }
    .side-panel {
        width: 300px;
        background: #1e293b;
        border: 1px solid #334155;
        border-radius: 14px;
        padding: 20px;
        box-shadow: 0 12px 30px rgba(0,0,0,0.5);
        display: flex;
        flex-direction: column;
        gap: 14px;
    }
    .panel-title {
        font-size: 18px;
        font-weight: 800;
        color: #fbbf24;
        border-bottom: 1px solid #334155;
        padding-bottom: 10px;
        margin: 0;
    }
    .status-box {
        background: #0f172a;
        border: 1px solid #334155;
        border-radius: 10px;
        padding: 14px;
        text-align: center;
    }
    .turn-indicator {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 10px;
        font-size: 17px;
        font-weight: 800;
    }
    .stone-icon {
        width: 22px;
        height: 22px;
        border-radius: 50%;
        display: inline-block;
    }
    .stone-black {
        background: radial-gradient(circle at 30% 30%, #555, #0a0a0a);
        border: 1px solid #475569;
    }
    .stone-white {
        background: radial-gradient(circle at 30% 30%, #ffffff, #cbd5e1);
        border: 1px solid #94a3b8;
    }
    .sub-status {
        margin-top: 8px;
        font-size: 13px;
        color: #94a3b8;
        min-height: 18px;
    }
    .forbidden-alert {
        color: #f87171;
        font-weight: 700;
    }
    .win-alert {
        color: #4ade80;
        font-weight: 800;
        font-size: 15px;
    }
    .control-group {
        display: flex;
        flex-direction: column;
        gap: 6px;
    }
    label {
        font-size: 12px;
        font-weight: 700;
        color: #cbd5e1;
    }
    select, button {
        width: 100%;
        padding: 10px 12px;
        border-radius: 8px;
        font-size: 14px;
        font-weight: 700;
        border: none;
        cursor: pointer;
        transition: all 0.15s;
    }
    select {
        background: #0f172a;
        color: #f8fafc;
        border: 1px solid #475569;
    }
    .btn-primary {
        background: #f59e0b;
        color: #0f172a;
    }
    .btn-primary:hover {
        background: #fbbf24;
    }
    .btn-secondary {
        background: #334155;
        color: #f8fafc;
    }
    .btn-secondary:hover {
        background: #475569;
    }
    .rule-card {
        background: rgba(15, 23, 42, 0.7);
        border-left: 3px solid #ef4444;
        padding: 10px 12px;
        border-radius: 6px;
        font-size: 12px;
        line-height: 1.55;
        color: #cbd5e1;
    }
    .score-row {
        display: flex;
        justify-content: space-between;
        font-size: 13px;
        background: #0f172a;
        padding: 8px 12px;
        border-radius: 8px;
    }
</style>
</head>
<body>

<div class="board-wrapper">
    <canvas id="omokCanvas" width="560" height="560"></canvas>
</div>

<div class="side-panel">
    <h2 class="panel-title">🎮 대국 설정 및 현황</h2>

    <div class="status-box">
        <div class="turn-indicator" id="turnDisplay">
            <span class="stone-icon stone-black"></span>
            <span>흑돌 (선공) 차례</span>
        </div>
        <div class="sub-status" id="subStatus">원하는 교차점을 클릭해 착수하세요.</div>
    </div>

    <div class="score-row">
        <span>총 수순: <b id="moveCount" style="color:#38bdf8;">0수</b></span>
        <span>금수 표시: <b style="color:#f87171;">✕ (흑 제한)</b></span>
    </div>

    <div class="control-group">
        <label>🕹️ 대국 모드 선택</label>
        <select id="gameMode" onchange="resetGame()">
            <option value="AI_BLACK" selected>🤖 AI 대전 (플레이어: 흑 / AI: 백)</option>
            <option value="AI_WHITE">🤖 AI 대전 (플레이어: 백 / AI: 흑)</option>
            <option value="PVP">👥 2인 대전 (한 화면에서 번갈아 두기)</option>
        </select>
    </div>

    <div style="display:flex; gap:8px;">
        <button class="btn-secondary" onclick="undoMove()">↩️ 무르기</button>
        <button class="btn-primary" onclick="resetGame()">🔄 새 대국</button>
    </div>

    <div class="rule-card">
        <b>📌 렌주룰(Renju) 핵심 요약</b><br>
        • <b>흑돌:</b> 정확한 <b>5목</b>만 승리<br>
        • <b>흑돌 금수:</b> 3-3, 4-4, 장목(6목 이상)<br>
        • <b>예외:</b> 금수 자리라도 5목 완성 시 승리<br>
        • <b>백돌:</b> 금수 없음 (5목 이상 모두 승리)
    </div>
</div>

<script>
const SIZE = 15;
const PADDING = 35;
const CELL = (560 - PADDING * 2) / (SIZE - 1); // 35px
const BLACK = 1;
const WHITE = 2;
const DIRS = [[0, 1], [1, 0], [1, 1], [1, -1]];

const canvas = document.getElementById('omokCanvas');
const ctx = canvas.getContext('2d');

let board = [];
let moveHistory = [];
let currentTurn = BLACK;
let gameOver = false;
let hoverPos = null;
let forbiddenMap = {}; // "r,c" -> reason string
let winningLine = [];
let aiThinking = false;

// --- Web Audio API 바둑돌 착수 효과음 ("딱!") ---
let audioCtx = null;
function playStoneSound() {
    try {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        const now = audioCtx.currentTime;

        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = 'triangle';
        osc.frequency.setValueAtTime(520, now);
        osc.frequency.exponentialRampToValueAtTime(110, now + 0.055);

        gain.gain.setValueAtTime(0.35, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.06);

        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start(now);
        osc.stop(now + 0.065);
    } catch (e) {}
}

// --- 초기화 ---
function resetGame() {
    board = Array.from({ length: SIZE }, () => Array(SIZE).fill(0));
    moveHistory = [];
    currentTurn = BLACK;
    gameOver = false;
    hoverPos = null;
    winningLine = [];
    aiThinking = false;

    updateForbiddenMap();
    updateUI();
    drawBoard();

    const mode = document.getElementById('gameMode').value;
    if (mode === 'AI_WHITE') {
        aiThinking = true;
        setTimeout(() => {
            makeAIMove();
            aiThinking = false;
        }, 300);
    }
}

function inBounds(r, c) {
    return r >= 0 && r < SIZE && c >= 0 && c < SIZE;
}

// 특정 방향(dr, dc) 연속 돌 개수 및 좌표 반환
function getLineInfo(r, c, dr, dc, color) {
    let stones = [[r, c]];
    let nr = r + dr, nc = c + dc;
    while (inBounds(nr, nc) && board[nr][nc] === color) {
        stones.push([nr, nc]);
        nr += dr;
        nc += dc;
    }
    nr = r - dr;
    nc = c - dc;
    while (inBounds(nr, nc) && board[nr][nc] === color) {
        stones.unshift([nr, nc]);
        nr -= dr;
        nc -= dc;
    }
    return stones;
}

// 정확한 5목 확인
function getExactFiveLine(r, c, color) {
    for (const [dr, dc] of DIRS) {
        const line = getLineInfo(r, c, dr, dc, color);
        if (line.length === 5) return line;
    }
    return null;
}

// 5목 이상(백돌 승리용) 확인
function getFiveOrMoreLine(r, c, color) {
    for (const [dr, dc] of DIRS) {
        const line = getLineInfo(r, c, dr, dc, color);
        if (line.length >= 5) return line;
    }
    return null;
}

// 장목(6목 이상) 확인
function hasOverline(r, c, color) {
    for (const [dr, dc] of DIRS) {
        if (getLineInfo(r, c, dr, dc, color).length >= 6) return true;
    }
    return false;
}

// 특정 축(dr, dc)에서 다음 한 수로 정확한 5목이 되는 빈칸 목록 찾기
function getFiveMakingSpotsOnAxis(r, c, dr, dc, color) {
    const spots = [];
    const seen = new Set();

    for (let step = -4; step <= 4; step++) {
        if (step === 0) continue;
        const er = r + dr * step;
        const ec = c + dc * step;
        if (!inBounds(er, ec) || board[er][ec] !== 0) continue;

        const key = `${er},${ec}`;
        if (seen.has(key)) continue;
        seen.add(key);

        board[er][ec] = color;
        const len = getLineInfo(er, ec, dr, dc, color).length;
        board[er][ec] = 0;

        if (len === 5) {
            spots.push([er, ec]);
        }
    }
    return spots;
}

// 흑돌의 '4' 개수 계산 (열린 4 및 닫힌 4 포함)
function countFours(r, c) {
    let fourCount = 0;
    for (const [dr, dc] of DIRS) {
        const spots = getFiveMakingSpotsOnAxis(r, c, dr, dc, BLACK);
        if (spots.length === 1) {
            fourCount += 1;
        } else if (spots.length >= 2) {
            // 두 지점 사이 거리가 5칸이면 하나의 열린 4(_BBBB_), 아니면 한 줄에 2개의 4(예: B_BB_B)
            const dist = Math.max(
                Math.abs(spots[0][0] - spots[1][0]),
                Math.abs(spots[0][1] - spots[1][1])
            );
            fourCount += (dist === 5) ? 1 : 2;
        }
    }
    return fourCount;
}

// 흑돌의 '열린 3(활삼)' 개수 계산 (재귀적으로 금수가 아닌 열린 4를 만들 수 있는지 검사)
function countOpenThrees(r, c) {
    let openThreeCount = 0;
    for (const [dr, dc] of DIRS) {
        let isAxisOpenThree = false;
        for (let step = -4; step <= 4; step++) {
            if (step === 0) continue;
            const er = r + dr * step;
            const ec = c + dc * step;
            if (!inBounds(er, ec) || board[er][ec] !== 0) continue;

            // (er, ec)에 두었을 때 해당 축에 양쪽이 열린 4(직선 4)가 생기는지 확인
            board[er][ec] = BLACK;
            const fiveSpots = getFiveMakingSpotsOnAxis(er, ec, dr, dc, BLACK);
            let makesStraightFour = false;
            if (fiveSpots.length === 2) {
                const dist = Math.max(
                    Math.abs(fiveSpots[0][0] - fiveSpots[1][0]),
                    Math.abs(fiveSpots[0][1] - fiveSpots[1][1])
                );
                if (dist === 5) makesStraightFour = true;
            }
            board[er][ec] = 0;

            if (makesStraightFour) {
                // 그 4를 만드는 수(er, ec) 자체가 금수가 아니어야 진정한 열린 3
                if (!checkBlackForbidden(er, ec)) {
                    isAxisOpenThree = true;
                    break;
                }
            }
        }
        if (isAxisOpenThree) openThreeCount++;
    }
    return openThreeCount;
}

// 흑돌 금수 판정 (null이면 착수 가능, 문자열이면 금수 사유 반환)
function checkBlackForbidden(r, c) {
    if (board[r][c] !== 0) return '이미 돌이 놓인 자리';

    board[r][c] = BLACK;

    // 1. 정확한 5목이 완성되면 금수 예외(즉시 승리)
    if (getExactFiveLine(r, c, BLACK)) {
        board[r][c] = 0;
        return null;
    }

    // 2. 장목 (6목 이상)
    if (hasOverline(r, c, BLACK)) {
        board[r][c] = 0;
        return '장목(6목 이상) 금수';
    }

    // 3. 4-4 금수
    if (countFours(r, c) >= 2) {
        board[r][c] = 0;
        return '4-4(사사) 금수';
    }

    // 4. 3-3 금수
    if (countOpenThrees(r, c) >= 2) {
        board[r][c] = 0;
        return '3-3(삼삼) 금수';
    }

    board[r][c] = 0;
    return null;
}

function updateForbiddenMap() {
    forbiddenMap = {};
    if (gameOver || currentTurn !== BLACK) return;
    for (let r = 0; r < SIZE; r++) {
        for (let c = 0; c < SIZE; c++) {
            if (board[r][c] === 0) {
                const reason = checkBlackForbidden(r, c);
                if (reason) forbiddenMap[`${r},${c}`] = reason;
            }
        }
    }
}

// --- 착수 처리 ---
function placeStone(r, c) {
    if (gameOver || !inBounds(r, c) || board[r][c] !== 0) return false;

    if (currentTurn === BLACK) {
        const forbiddenReason = checkBlackForbidden(r, c);
        if (forbiddenReason) {
            setSubStatus(`⛔ 착수 불가: ${forbiddenReason}`, 'forbidden-alert');
            return false;
        }
    }

    board[r][c] = currentTurn;
    moveHistory.push({ r, c, color: currentTurn });
    playStoneSound();

    // 승리 판정
    const winLine = (currentTurn === BLACK)
        ? getExactFiveLine(r, c, BLACK)
        : getFiveOrMoreLine(r, c, WHITE);

    if (winLine) {
        gameOver = true;
        winningLine = winLine;
        forbiddenMap = {};
        updateUI(currentTurn === BLACK ? '🎉 흑돌 승리! (정확한 5목 완성)' : '🎉 백돌 승리! (5목 이상 완성)');
        drawBoard();
        return true;
    }

    // 무승부 판정 (225수 가득 참)
    if (moveHistory.length === SIZE * SIZE) {
        gameOver = true;
        forbiddenMap = {};
        updateUI('🤝 무승부! 모든 칸이 찼습니다.');
        drawBoard();
        return true;
    }

    // 턴 교대
    currentTurn = (currentTurn === BLACK) ? WHITE : BLACK;
    updateForbiddenMap();
    updateUI();
    drawBoard();
    return true;
}

// --- 무르기 ---
function undoMove() {
    if (aiThinking || moveHistory.length === 0) return;
    const mode = document.getElementById('gameMode').value;

    if (mode === 'PVP') {
        const last = moveHistory.pop();
        board[last.r][last.c] = 0;
        currentTurn = last.color;
    } else {
        // AI 대전 시 AI 수 + 내 수까지 되돌리기
        const playerColor = (mode === 'AI_BLACK') ? BLACK : WHITE;
        if (moveHistory.length === 1 && playerColor === WHITE) return;

        while (moveHistory.length > 0) {
            const last = moveHistory.pop();
            board[last.r][last.c] = 0;
            currentTurn = last.color;
            if (last.color === playerColor) break;
        }
    }

    gameOver = false;
    winningLine = [];
    updateForbiddenMap();
    updateUI('무르기를 실행했습니다.');
    drawBoard();
}

// --- 스마트 휴리스틱 AI ---
function evaluatePointForColor(r, c, color) {
    board[r][c] = color;
    let score = 0;

    if (color === BLACK) {
        if (getExactFiveLine(r, c, BLACK)) {
            board[r][c] = 0;
            return 1000000;
        }
    } else {
        if (getFiveOrMoreLine(r, c, WHITE)) {
            board[r][c] = 0;
            return 1000000;
        }
    }

    for (const [dr, dc] of DIRS) {
        const line = getLineInfo(r, c, dr, dc, color);
        const len = line.length;
        const [sr, sc] = line[0];
        const [er, ec] = line[line.length - 1];

        const openStart = inBounds(sr - dr, sc - dc) && board[sr - dr][sc - dc] === 0;
        const openEnd = inBounds(er + dr, ec + dc) && board[er + dr][ec + dc] === 0;
        const openEnds = (openStart ? 1 : 0) + (openEnd ? 1 : 0);

        if (len === 4) {
            if (openEnds === 2) score += 50000;
            else if (openEnds === 1) score += 8000;
        } else if (len === 3) {
            if (openEnds === 2) score += 6000;
            else if (openEnds === 1) score += 800;
        } else if (len === 2) {
            if (openEnds === 2) score += 400;
            else if (openEnds === 1) score += 80;
        } else if (len === 1 && openEnds === 2) {
            score += 20;
        }
    }

    board[r][c] = 0;
    return score;
}

function makeAIMove() {
    if (gameOver) return;
    if (moveHistory.length === 0) {
        placeStone(7, 7); // 천원(중앙) 첫 수
        return;
    }

    const aiColor = currentTurn;
    const oppColor = (aiColor === BLACK) ? WHITE : BLACK;
    let bestScore = -Infinity;
    let bestMoves = [];

    for (let r = 0; r < SIZE; r++) {
        for (let c = 0; c < SIZE; c++) {
            if (board[r][c] !== 0) continue;
            if (aiColor === BLACK && forbiddenMap[`${r},${c}`]) continue;

            const attack = evaluatePointForColor(r, c, aiColor);
            // 상대가 흑일 때 금수 자리라면 방어할 필요 없음
            const oppForbidden = (oppColor === BLACK && checkBlackForbidden(r, c));
            const defense = oppForbidden ? 0 : evaluatePointForColor(r, c, oppColor);

            const centerDist = Math.abs(r - 7) + Math.abs(c - 7);
            const total = attack * 1.15 + defense - centerDist * 2;

            if (total > bestScore) {
                bestScore = total;
                bestMoves = [{ r, c }];
            } else if (Math.abs(total - bestScore) < 1) {
                bestMoves.push({ r, c });
            }
        }
    }

    if (bestMoves.length > 0) {
        const pick = bestMoves[Math.floor(Math.random() * bestMoves.length)];
        placeStone(pick.r, pick.c);
    }
}

// --- UI 업데이트 ---
function setSubStatus(text, className = '') {
    const el = document.getElementById('subStatus');
    el.innerText = text;
    el.className = 'sub-status ' + className;
}

function updateUI(customMsg = null) {
    const turnEl = document.getElementById('turnDisplay');
    document.getElementById('moveCount').innerText = `${moveHistory.length}수`;

    if (gameOver) {
        setSubStatus(customMsg || '대국이 종료되었습니다.', 'win-alert');
        return;
    }

    if (currentTurn === BLACK) {
        turnEl.innerHTML = `<span class="stone-icon stone-black"></span><span>흑돌 (선공) 차례</span>`;
    } else {
        turnEl.innerHTML = `<span class="stone-icon stone-white"></span><span>백돌 (후공) 차례</span>`;
    }

    if (customMsg) {
        setSubStatus(customMsg);
    } else {
        setSubStatus('교차점을 클릭하여 착수하세요.');
    }
}

// --- 캔버스 렌더링 ---
function drawBoard() {
    // 1. 나무 바둑판 배경
    const woodGrad = ctx.createLinearGradient(0, 0, 560, 560);
    woodGrad.addColorStop(0, '#e3b36b');
    woodGrad.addColorStop(0.5, '#d49b4b');
    woodGrad.addColorStop(1, '#c58939');
    ctx.fillStyle = woodGrad;
    ctx.fillRect(0, 0, 560, 560);

    // 2. 15x15 격자선
    ctx.strokeStyle = '#3b240e';
    ctx.lineWidth = 1.2;
    for (let i = 0; i < SIZE; i++) {
        const pos = Math.round(PADDING + i * CELL) + 0.5;
        ctx.beginPath();
        ctx.moveTo(PADDING, pos);
        ctx.lineTo(560 - PADDING, pos);
        ctx.stroke();

        ctx.beginPath();
        ctx.moveTo(pos, PADDING);
        ctx.lineTo(pos, 560 - PADDING);
        ctx.stroke();
    }

    // 3. 화점 5개 (천원 및 귀 화점)
    const starPoints = [[3, 3], [3, 11], [7, 7], [11, 3], [11, 11]];
    ctx.fillStyle = '#291707';
    for (const [sr, sc] of starPoints) {
        ctx.beginPath();
        ctx.arc(PADDING + sc * CELL, PADDING + sr * CELL, 4, 0, Math.PI * 2);
        ctx.fill();
    }

    // 4. 흑돌 금수 위치 붉은색 X 마커
    if (!gameOver && currentTurn === BLACK) {
        ctx.strokeStyle = 'rgba(220, 38, 38, 0.85)';
        ctx.lineWidth = 2.4;
        for (const key of Object.keys(forbiddenMap)) {
            const [fr, fc] = key.split(',').map(Number);
            const x = PADDING + fc * CELL;
            const y = PADDING + fr * CELL;
            const d = 6;
            ctx.beginPath();
            ctx.moveTo(x - d, y - d);
            ctx.lineTo(x + d, y + d);
            ctx.moveTo(x + d, y - d);
            ctx.lineTo(x - d, y + d);
            ctx.stroke();
        }
    }

    // 5. 마우스 호버 미리보기
    if (!gameOver && !aiThinking && hoverPos) {
        const { r, c } = hoverPos;
        if (board[r][c] === 0 && !(currentTurn === BLACK && forbiddenMap[`${r},${c}`])) {
            const x = PADDING + c * CELL;
            const y = PADDING + r * CELL;
            ctx.beginPath();
            ctx.arc(x, y, CELL * 0.43, 0, Math.PI * 2);
            ctx.fillStyle = currentTurn === BLACK ? 'rgba(15, 15, 15, 0.4)' : 'rgba(255, 255, 255, 0.5)';
            ctx.fill();
        }
    }

    // 6. 착수된 흑돌 / 백돌 렌더링
    for (let r = 0; r < SIZE; r++) {
        for (let c = 0; c < SIZE; c++) {
            if (board[r][c] === 0) continue;
            const x = PADDING + c * CELL;
            const y = PADDING + r * CELL;
            const radius = CELL * 0.44;

            // 그림자
            ctx.beginPath();
            ctx.arc(x + 2, y + 2.5, radius, 0, Math.PI * 2);
            ctx.fillStyle = 'rgba(0, 0, 0, 0.35)';
            ctx.fill();

            // 돌 본체
            const grad = ctx.createRadialGradient(
                x - radius * 0.3, y - radius * 0.3, radius * 0.1,
                x, y, radius
            );
            if (board[r][c] === BLACK) {
                grad.addColorStop(0, '#52525b');
                grad.addColorStop(0.35, '#18181b');
                grad.addColorStop(1, '#09090b');
            } else {
                grad.addColorStop(0, '#ffffff');
                grad.addColorStop(0.7, '#e2e8f0');
                grad.addColorStop(1, '#94a3b8');
            }

            ctx.beginPath();
            ctx.arc(x, y, radius, 0, Math.PI * 2);
            ctx.fillStyle = grad;
            ctx.fill();
        }
    }

    // 7. 마지막 착수 위치 빨간 점 강조
    if (moveHistory.length > 0) {
        const last = moveHistory[moveHistory.length - 1];
        const lx = PADDING + last.c * CELL;
        const ly = PADDING + last.r * CELL;
        ctx.beginPath();
        ctx.arc(lx, ly, 4, 0, Math.PI * 2);
        ctx.fillStyle = '#ef4444';
        ctx.fill();
    }

    // 8. 승리한 5목 연결선 강조
    if (winningLine.length >= 5) {
        const first = winningLine[0];
        const last = winningLine[winningLine.length - 1];
        ctx.beginPath();
        ctx.moveTo(PADDING + first[1] * CELL, PADDING + first[0] * CELL);
        ctx.lineTo(PADDING + last[1] * CELL, PADDING + last[0] * CELL);
        ctx.strokeStyle = 'rgba(239, 68, 68, 0.85)';
        ctx.lineWidth = 5;
        ctx.lineCap = 'round';
        ctx.stroke();
    }
}

// --- 마우스 좌표 변환 및 이벤트 ---
function getGridFromMouse(e) {
    const rect = canvas.getBoundingClientRect();
    const scaleX = canvas.width / rect.width;
    const scaleY = canvas.height / rect.height;
    const mx = (e.clientX - rect.left) * scaleX;
    const my = (e.clientY - rect.top) * scaleY;

    const c = Math.round((mx - PADDING) / CELL);
    const r = Math.round((my - PADDING) / CELL);

    if (!inBounds(r, c)) return null;
    const dist = Math.hypot(mx - (PADDING + c * CELL), my - (PADDING + r * CELL));
    return dist <= CELL * 0.48 ? { r, c } : null;
}

canvas.addEventListener('mousemove', (e) => {
    const pos = getGridFromMouse(e);
    hoverPos = pos;

    if (!gameOver && !aiThinking && pos && currentTurn === BLACK) {
        const reason = forbiddenMap[`${pos.r},${pos.c}`];
        if (reason) {
            setSubStatus(`⚠️ 금수 구역: ${reason}`, 'forbidden-alert');
        } else {
            setSubStatus('교차점을 클릭하여 착수하세요.');
        }
    }
    drawBoard();
});

canvas.addEventListener('mouseleave', () => {
    hoverPos = null;
    drawBoard();
});

canvas.addEventListener('click', (e) => {
    if (gameOver || aiThinking) return;
    const pos = getGridFromMouse(e);
    if (!pos) return;

    const mode = document.getElementById('gameMode').value;
    if (mode === 'AI_BLACK' && currentTurn !== BLACK) return;
    if (mode === 'AI_WHITE' && currentTurn !== WHITE) return;

    const moved = placeStone(pos.r, pos.c);
    if (moved && !gameOver && mode !== 'PVP') {
        aiThinking = true;
        setSubStatus('🤖 AI가 다음 수를 계산 중입니다...');
        setTimeout(() => {
            makeAIMove();
            aiThinking = false;
        }, 280);
    }
});

resetGame();
</script>
</body>
</html>
"""

components.html(omok_html, height=610)
