// Cricket Scorecard Application
class CricketScorecard {
    constructor() {
        this.players = [];
        this.matchOvers = 20;
        this.targetRuns = null;
        this.currentScore = {
            runs: 0,
            wickets: 0,
            overs: 0,
            balls: 0,
            extras: {
                wide: 0,
                noBall: 0,
                bye: 0,
                legBye: 0
            }
        };
        this.batsmen = [];
        this.bowlers = [];
        this.currentOver = [];
        this.actionHistory = [];
        this.currentStriker = null;
        this.currentNonStriker = null;
        this.currentBowler = null;
        this.nextBowler = null;
        this.isFirstInnings = true;
        this.matchStarted = false;
        
        this.init();
    }
    
    init() {
        // Load saved data if exists
        this.loadFromLocalStorage();
        
        // Initialize event listeners
        this.initEventListeners();
        
        // Show setup modal on start
        this.showSetupModal();
    }
    
    initEventListeners() {
        // Setup modal buttons
        document.getElementById('addPlayerBtn').addEventListener('click', () => this.addPlayer());
        document.getElementById('startMatchBtn').addEventListener('click', () => this.startMatch());
        
        // Main buttons
        document.getElementById('undoBtn').addEventListener('click', () => this.undoLastAction());
        document.getElementById('resetBtn').addEventListener('click', () => this.resetMatch());
        document.getElementById('changeStrikeBtn').addEventListener('click', () => this.changeStrike());
        
        // Run buttons
        document.querySelectorAll('.run-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const runs = parseInt(e.target.dataset.runs);
                this.addRuns(runs);
            });
        });
        
        // Extra buttons
        document.querySelectorAll('.extra-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const extra = e.target.dataset.extra;
                this.addExtra(extra);
            });
        });
        
        // Wicket button
        document.getElementById('wicketBtn').addEventListener('click', () => this.showWicketModal());
        
        // Wicket modal
        document.querySelectorAll('.wicket-type-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                document.querySelectorAll('.wicket-type-btn').forEach(b => b.classList.remove('active'));
                e.target.classList.add('active');
                const wicketType = e.target.dataset.type;
                
                // Show fielder selection for certain wicket types
                const fielderSection = document.getElementById('wicketDetails');
                if (['caught', 'runOut', 'stumped'].includes(wicketType)) {
                    fielderSection.style.display = 'block';
                    this.populateFielderSelect();
                } else {
                    fielderSection.style.display = 'none';
                }
            });
        });
        
        document.getElementById('cancelWicketBtn').addEventListener('click', () => this.hideWicketModal());
        document.getElementById('confirmWicketBtn').addEventListener('click', () => this.confirmWicket());
        
        // Player selection change listeners
        document.getElementById('strikerSelect').addEventListener('change', (e) => this.setStriker(e.target.value));
        document.getElementById('nonStrikerSelect').addEventListener('change', (e) => this.setNonStriker(e.target.value));
        document.getElementById('currentBowlerSelect').addEventListener('change', (e) => this.setCurrentBowler(e.target.value));
        document.getElementById('nextBowlerSelect').addEventListener('change', (e) => this.setNextBowler(e.target.value));
        document.getElementById('newBatsmanSelect').addEventListener('change', (e) => this.setNewBatsman(e.target.value));
        
        // Save data on page unload
        window.addEventListener('beforeunload', () => this.saveToLocalStorage());
        
        // Keyboard shortcuts
        document.addEventListener('keydown', (e) => {
            if (!this.matchStarted) return;
            
            // Number keys for runs
            if (e.key >= '0' && e.key <= '6') {
                e.preventDefault();
                this.addRuns(parseInt(e.key));
            }
            
            // W for wicket
            if (e.key.toLowerCase() === 'w') {
                e.preventDefault();
                this.showWicketModal();
            }
            
            // Space for change strike
            if (e.key === ' ') {
                e.preventDefault();
                this.changeStrike();
            }
            
            // U for undo
            if (e.key.toLowerCase() === 'u') {
                e.preventDefault();
                this.undoLastAction();
            }
            
            // Z for undo (alternative)
            if (e.key.toLowerCase() === 'z' && e.ctrlKey) {
                e.preventDefault();
                this.undoLastAction();
            }
        });
    }
    
    showSetupModal() {
        document.getElementById('setupModal').style.display = 'flex';
        document.getElementById('scoreboard').style.display = 'none';
    }
    
    hideSetupModal() {
        document.getElementById('setupModal').style.display = 'none';
        document.getElementById('scoreboard').style.display = 'block';
    }
    
    addPlayer() {
        const nameInput = document.getElementById('playerName');
        const typeSelect = document.getElementById('playerType');
        
        const name = nameInput.value.trim();
        const type = typeSelect.value;
        
        if (!name) {
            alert('Please enter player name');
            return;
        }
        
        // Check if player already exists
        if (this.players.some(p => p.name.toLowerCase() === name.toLowerCase())) {
            alert('Player with this name already exists');
            return;
        }
        
        const player = {
            id: Date.now(),
            name: name,
            type: type,
            battingStats: {
                runs: 0,
                balls: 0,
                fours: 0,
                sixes: 0,
                isOut: false,
                outType: null,
                fielder: null,
                striker: false
            },
            bowlingStats: {
                overs: 0,
                balls: 0,
                maidens: 0,
                runs: 0,
                wickets: 0,
                wides: 0,
                noBalls: 0,
                isBowler: false
            }
        };
        
        this.players.push(player);
        this.renderPlayerList();
        
        // Clear input
        nameInput.value = '';
        nameInput.focus();
    }
    
    renderPlayerList() {
        const playerList = document.getElementById('playerList');
        playerList.innerHTML = '';
        
        this.players.forEach(player => {
            const tag = document.createElement('div');
            tag.className = 'player-tag';
            tag.innerHTML = `
                <i class="fas fa-${player.type === 'batsman' ? 'bat' : 
                                   player.type === 'bowler' ? 'ball' : 'user' }"></i>
                ${player.name}
                <span class="player-type">(${player.type})</span>
                <i class="fas fa-times remove-player" data-id="${player.id}"></i>
            `;
            playerList.appendChild(tag);
        });
        
        // Add remove event listeners
        document.querySelectorAll('.remove-player').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const id = parseInt(e.target.dataset.id);
                this.removePlayer(id);
            });
        });
        
        // Update player selects
        this.populatePlayerSelects();
    }
    
    removePlayer(id) {
        this.players = this.players.filter(p => p.id !== id);
        this.renderPlayerList();
    }
    
    populatePlayerSelects() {
        const strikerSelect = document.getElementById('strikerSelect');
        const nonStrikerSelect = document.getElementById('nonStrikerSelect');
        const newBatsmanSelect = document.getElementById('newBatsmanSelect');
        const currentBowlerSelect = document.getElementById('currentBowlerSelect');
        const nextBowlerSelect = document.getElementById('nextBowlerSelect');
        
        // Clear all selects
        [strikerSelect, nonStrikerSelect, newBatsmanSelect, currentBowlerSelect, nextBowlerSelect].forEach(select => {
            select.innerHTML = '<option value="">Select player</option>';
        });
        
        // Add players to batting selects
        this.players.forEach(player => {
            const option = document.createElement('option');
            option.value = player.id;
            option.textContent = player.name;
            
            strikerSelect.appendChild(option.cloneNode(true));
            nonStrikerSelect.appendChild(option.cloneNode(true));
            newBatsmanSelect.appendChild(option.cloneNode(true));
        });
        
        // Add bowlers to bowling selects
        this.players.filter(p => p.type === 'bowler' || p.type === 'allrounder').forEach(player => {
            const option = document.createElement('option');
            option.value = player.id;
            option.textContent = player.name;
            
            currentBowlerSelect.appendChild(option.cloneNode(true));
            nextBowlerSelect.appendChild(option.cloneNode(true));
        });
    }
    
    populateFielderSelect() {
        const fielderSelect = document.getElementById('fielderSelect');
        fielderSelect.innerHTML = '<option value="">Select fielder</option>';
        
        this.players.forEach(player => {
            const option = document.createElement('option');
            option.value = player.id;
            option.textContent = player.name;
            fielderSelect.appendChild(option);
        });
    }
    
    startMatch() {
        if (this.players.length < 2) {
            alert('Please add at least 2 players to start the match');
            return;
        }
        
        const teamName = document.getElementById('teamName').value.trim() || 'Local Team';
        this.matchOvers = parseInt(document.getElementById('overs').value);
        const target = document.getElementById('targetRuns').value;
        this.targetRuns = target ? parseInt(target) : null;
        this.isFirstInnings = !this.targetRuns;
        
        // Initialize match state
        this.matchStarted = true;
        this.currentScore = {
            runs: 0,
            wickets: 0,
            overs: 0,
            balls: 0,
            extras: {
                wide: 0,
                noBall: 0,
                bye: 0,
                legBye: 0
            }
        };
        this.currentOver = [];
        this.actionHistory = [];
        
        // Initialize batsmen and bowlers
        this.batsmen = this.players.map(p => ({ ...p.battingStats, playerId: p.id, name: p.name }));
        this.bowlers = this.players.filter(p => p.type === 'bowler' || p.type === 'allrounder')
            .map(p => ({ ...p.bowlingStats, playerId: p.id, name: p.name }));
        
        // Update UI
        document.getElementById('teamDisplay').textContent = teamName;
        document.getElementById('oversInfo').textContent = `${this.matchOvers} Overs Match`;
        
        if (this.targetRuns) {
            document.getElementById('targetInfo').textContent = `Target: ${this.targetRuns}`;
            document.getElementById('targetInfo').style.display = 'inline';
        } else {
            document.getElementById('targetInfo').style.display = 'none';
        }
        
        this.updateScoreboard();
        this.hideSetupModal();
        
        // Request player selections
        alert('Please select opening batsmen and current bowler from the dropdown menus');
    }
    
    setStriker(playerId) {
        if (!playerId) return;
        
        const player = this.players.find(p => p.id === parseInt(playerId));
        if (!player) return;
        
        this.currentStriker = playerId;
        
        // Update all batsmen
        this.batsmen.forEach(b => {
            b.striker = (b.playerId === parseInt(playerId));
        });
        
        this.updateBattingStats();
    }
    
    setNonStriker(playerId) {
        if (!playerId) return;
        
        this.currentNonStriker = playerId;
        this.updateBattingStats();
    }
    
    setCurrentBowler(playerId) {
        if (!playerId) return;
        
        const player = this.players.find(p => p.id === parseInt(playerId));
        if (!player) return;
        
        this.currentBowler = playerId;
        document.getElementById('currentBowlerName').textContent = player.name;
        
        // Mark bowler as active
        this.bowlers.forEach(b => {
            b.isBowler = (b.playerId === parseInt(playerId));
        });
        
        this.updateBowlingStats();
    }
    
    setNextBowler(playerId) {
        this.nextBowler = playerId;
    }
    
    setNewBatsman(playerId) {
        if (!playerId || !this.currentStriker) return;
        
        const newBatsman = this.players.find(p => p.id === parseInt(playerId));
        if (!newBatsman) return;
        
        // Find the out batsman
        const outBatsmanIndex = this.batsmen.findIndex(b => 
            b.playerId === this.currentStriker && b.isOut);
        
        if (outBatsmanIndex !== -1) {
            // Replace the out batsman
            this.batsmen[outBatsmanIndex] = {
                ...newBatsman.battingStats,
                playerId: newBatsman.id,
                name: newBatsman.name,
                striker: true
            };
            
            this.currentStriker = newBatsman.id;
            document.getElementById('strikerSelect').value = newBatsman.id;
            document.getElementById('newBatsmanSelect').value = '';
            
            this.updateBattingStats();
            this.addActionToLog('New batsman', `${newBatsman.name} comes to the crease`);
        }
    }
    
    addRuns(runs) {
        if (!this.validateAction()) return;
        
        // Update score
        this.currentScore.runs += runs;
        this.currentScore.balls++;
        
        // Update striker stats
        const striker = this.batsmen.find(b => b.playerId === this.currentStriker);
        if (striker) {
            striker.runs += runs;
            striker.balls++;
            if (runs === 4) striker.fours++;
            if (runs === 6) striker.sixes++;
        }
        
        // Update bowler stats
        const bowler = this.bowlers.find(b => b.playerId === this.currentBowler);
        if (bowler) {
            bowler.runs += runs;
            bowler.balls++;
        }
        
        // Add to current over
        this.currentOver.push({
            type: 'run',
            value: runs,
            batsmanId: this.currentStriker,
            bowlerId: this.currentBowler
        });
        
        // Change strike on odd runs (except for last ball of over)
        if (runs % 2 === 1 && this.currentScore.balls !== 6) {
            this.changeStrike();
        }
        
        // Check over completion
        this.checkOverCompletion();
        
        // Add to action log
        const strikerName = this.players.find(p => p.id === this.currentStriker)?.name || 'Unknown';
        this.addActionToLog('Run', `${runs} run${runs !== 1 ? 's' : ''} by ${strikerName}`);
        
        this.updateScoreboard();
        this.saveToLocalStorage();
    }
    
    addExtra(type) {
        if (!this.validateAction()) return;
        
        let extraRuns = 1;
        let isBowlerResponsible = false;
        
        switch(type) {
            case 'wide':
                this.currentScore.extras.wide++;
                extraRuns = 1;
                isBowlerResponsible = true;
                break;
            case 'noBall':
                this.currentScore.extras.noBall++;
                extraRuns = 2; // 1 for no ball + 1 run
                isBowlerResponsible = true;
                break;
            case 'bye':
                this.currentScore.extras.bye++;
                extraRuns = 1;
                break;
            case 'legBye':
                this.currentScore.extras.legBye++;
                extraRuns = 1;
                break;
        }
        
        // Update score
        this.currentScore.runs += extraRuns;
        
        // No ball doesn't count as a ball
        if (type !== 'noBall') {
            this.currentScore.balls++;
        }
        
        // Update bowler stats for wides and no balls
        if (isBowlerResponsible) {
            const bowler = this.bowlers.find(b => b.playerId === this.currentBowler);
            if (bowler) {
                bowler.runs += extraRuns;
                if (type === 'wide') bowler.wides++;
                if (type === 'noBall') bowler.noBalls++;
            }
        }
        
        // Add to current over
        this.currentOver.push({
            type: 'extra',
            value: type,
            runs: extraRuns
        });
        
        // Check over completion (except for no balls)
        if (type !== 'noBall') {
            this.checkOverCompletion();
        }
        
        // Add to action log
        this.addActionToLog('Extra', `${type} - ${extraRuns} run${extraRuns !== 1 ? 's' : ''}`);
        
        this.updateScoreboard();
        this.saveToLocalStorage();
    }
    
    showWicketModal() {
        if (!this.validateAction()) return;
        
        document.getElementById('wicketModal').style.display = 'flex';
        document.getElementById('wicketDetails').style.display = 'none';
        
        // Reset selections
        document.querySelectorAll('.wicket-type-btn').forEach(b => b.classList.remove('active'));
        document.getElementById('fielderSelect').value = '';
    }
    
    hideWicketModal() {
        document.getElementById('wicketModal').style.display = 'none';
    }
    
    confirmWicket() {
        const activeBtn = document.querySelector('.wicket-type-btn.active');
        if (!activeBtn) {
            alert('Please select a wicket type');
            return;
        }
        
        const wicketType = activeBtn.dataset.type;
        const fielderId = document.getElementById('fielderSelect').value;
        const fielder = fielderId ? this.players.find(p => p.id === parseInt(fielderId)) : null;
        
        this.addWicket(wicketType, fielder);
        this.hideWicketModal();
    }
    
    addWicket(type, fielder = null) {
        // Update score
        this.currentScore.wickets++;
        this.currentScore.balls++;
        
        // Mark striker as out
        const strikerIndex = this.batsmen.findIndex(b => b.playerId === this.currentStriker);
        if (strikerIndex !== -1) {
            this.batsmen[strikerIndex].isOut = true;
            this.batsmen[strikerIndex].outType = type;
            this.batsmen[strikerIndex].fielder = fielder?.name;
            this.batsmen[strikerIndex].striker = false;
        }
        
        // Update bowler stats
        const bowler = this.bowlers.find(b => b.playerId === this.currentBowler);
        if (bowler) {
            bowler.wickets++;
            bowler.balls++;
        }
        
        // Add to current over
        this.currentOver.push({
            type: 'wicket',
            value: type,
            batsmanId: this.currentStriker,
            bowlerId: this.currentBowler,
            fielderId: fielder?.id
        });
        
        // Add to action log
        const batsmanName = this.players.find(p => p.id === this.currentStriker)?.name || 'Unknown';
        const bowlerName = this.players.find(p => p.id === this.currentBowler)?.name || 'Unknown';
        
        let wicketDescription = `${batsmanName} ${type.toLowerCase()} b ${bowlerName}`;
        if (fielder) {
            wicketDescription += ` c ${fielder.name}`;
        }
        
        this.addActionToLog('Wicket', wicketDescription);
        
        // Check if all out
        if (this.currentScore.wickets >= this.players.length - 1) {
            this.endInnings();
        } else {
            // Prompt for new batsman
            document.getElementById('newBatsmanSelect').value = '';
            alert(`${batsmanName} is out! Please select a new batsman from the dropdown.`);
        }
        
        this.checkOverCompletion();
        this.updateScoreboard();
        this.saveToLocalStorage();
    }
    
    changeStrike() {
        if (!this.currentStriker || !this.currentNonStriker) return;
        
        // Swap striker and non-striker
        const temp = this.currentStriker;
        this.currentStriker = this.currentNonStriker;
        this.currentNonStriker = temp;
        
        // Update batsmen
        this.batsmen.forEach(b => {
            b.striker = (b.playerId === this.currentStriker);
        });
        
        document.getElementById('strikerSelect').value = this.currentStriker;
        document.getElementById('nonStrikerSelect').value = this.currentNonStriker;
        
        this.addActionToLog('Strike Change', 'Batsmen changed ends');
        this.updateBattingStats();
    }
    
    checkOverCompletion() {
        if (this.currentScore.balls === 6) {
            // Over completed
            this.currentScore.overs++;
            this.currentScore.balls = 0;
            
            // Change strike (batsmen cross ends)
            this.changeStrike();
            
            // Change bowler if next bowler is selected
            if (this.nextBowler) {
                this.setCurrentBowler(this.nextBowler);
                this.nextBowler = null;
                document.getElementById('nextBowlerSelect').value = '';
            }
            
            // Reset current over
            this.currentOver = [];
            
            this.addActionToLog('Over Complete', `Over ${this.currentScore.overs} completed`);
            
            // Check if innings is complete
            if (this.currentScore.overs >= this.matchOvers) {
                this.endInnings();
            }
        }
        
        this.updateOverDisplay();
    }
    
    endInnings() {
        let message = 'Innings Complete! ';
        
        if (this.isFirstInnings) {
            message += `Final Score: ${this.currentScore.runs}/${this.currentScore.wickets}`;
            this.isFirstInnings = false;
            
            // If chasing, set target
            if (!this.targetRuns) {
                this.targetRuns = this.currentScore.runs + 1;
                document.getElementById('targetInfo').textContent = `Target: ${this.targetRuns}`;
                document.getElementById('targetInfo').style.display = 'inline';
            }
            
            // Reset for second innings
            this.currentScore = {
                runs: 0,
                wickets: 0,
                overs: 0,
                balls: 0,
                extras: {
                    wide: 0,
                    noBall: 0,
                    bye: 0,
                    legBye: 0
                }
            };
            
            // Reset batsmen stats but keep players
            this.batsmen = this.players.map(p => ({ 
                ...p.battingStats, 
                playerId: p.id, 
                name: p.name 
            }));
            
            // Reset current over
            this.currentOver = [];
            
            // Reset selections
            this.currentStriker = null;
            this.currentNonStriker = null;
            document.getElementById('strikerSelect').value = '';
            document.getElementById('nonStrikerSelect').value = '';
            
            alert('First innings complete! Please select opening batsmen for second innings.');
            
        } else {
            // Match complete
            if (this.currentScore.runs >= this.targetRuns) {
                message += `Team won by ${10 - this.currentScore.wickets} wickets!`;
            } else {
                message += `Team lost by ${this.targetRuns - this.currentScore.runs - 1} runs!`;
            }
            
            this.matchStarted = false;
        }
        
        this.addActionToLog('Innings', message);
        this.updateScoreboard();
        alert(message);
    }
    
    undoLastAction() {
        if (this.actionHistory.length === 0) return;
        
        const lastAction = this.actionHistory.pop();
        
        // For now, just remove from log
        // In a full implementation, you would reverse the action
        
        this.updateRecentActions();
        this.addActionToLog('System', 'Last action undone');
    }
    
    resetMatch() {
        if (!confirm('Are you sure you want to reset the match? All data will be lost.')) {
            return;
        }
        
        this.matchStarted = false;
        this.showSetupModal();
        localStorage.removeItem('cricketScorecardData');
    }
    
    validateAction() {
        if (!this.matchStarted) {
            alert('Please start the match first');
            return false;
        }
        
        if (!this.currentStriker || !this.currentNonStriker) {
            alert('Please select both batsmen');
            return false;
        }
        
        if (!this.currentBowler) {
            alert('Please select current bowler');
            return false;
        }
        
        if (this.currentStriker === this.currentNonStriker) {
            alert('Striker and non-striker cannot be the same player');
            return false;
        }
        
        return true;
    }
    
    updateScoreboard() {
        // Update main score
        document.getElementById('scoreRuns').textContent = this.currentScore.runs;
        document.getElementById('scoreWickets').textContent = this.currentScore.wickets;
        document.getElementById('currentOvers').textContent = `${this.currentScore.overs}.${this.currentScore.balls}`;
        
        // Calculate CRR
        const totalBalls = this.currentScore.overs * 6 + this.currentScore.balls;
        const crr = totalBalls > 0 ? (this.currentScore.runs / (totalBalls / 6)).toFixed(2) : '0.00';
        document.getElementById('crrValue').textContent = crr;
        
        // Calculate RRR if chasing
        if (this.targetRuns && !this.isFirstInnings) {
            const runsNeeded = this.targetRuns - this.currentScore.runs;
            const ballsRemaining = this.matchOvers * 6 - totalBalls;
            
            if (ballsRemaining > 0 && runsNeeded > 0) {
                const rrr = (runsNeeded / (ballsRemaining / 6)).toFixed(2);
                document.getElementById('rrrValue').textContent = rrr;
            } else {
                document.getElementById('rrrValue').textContent = '-';
            }
            
            document.getElementById('ballsLeft').textContent = ballsRemaining;
        } else {
            document.getElementById('rrrValue').textContent = '-';
            const totalBalls = this.matchOvers * 6;
            const ballsBowled = this.currentScore.overs * 6 + this.currentScore.balls;
            document.getElementById('ballsLeft').textContent = totalBalls - ballsBowled;
        }
        
        // Update extras
        document.getElementById('wdCount').textContent = this.currentScore.extras.wide;
        document.getElementById('nbCount').textContent = this.currentScore.extras.noBall;
        document.getElementById('bCount').textContent = this.currentScore.extras.bye;
        document.getElementById('lbCount').textContent = this.currentScore.extras.legBye;
        
        const totalExtras = Object.values(this.currentScore.extras).reduce((a, b) => a + b, 0);
        document.getElementById('totalExtras').textContent = totalExtras;
        
        // Update stats tables
        this.updateBattingStats();
        this.updateBowlingStats();
        this.updateOverDisplay();
        this.updateRecentActions();
    }
    
    updateBattingStats() {
        const tbody = document.getElementById('battingStatsBody');
        tbody.innerHTML = '';
        
        this.batsmen.forEach(batsman => {
            const player = this.players.find(p => p.id === batsman.playerId);
            if (!player) return;
            
            const tr = document.createElement('tr');
            if (batsman.striker) tr.classList.add('striker');
            if (batsman.isOut) tr.classList.add('out');
            
            const strikeRate = batsman.balls > 0 ? ((batsman.runs / batsman.balls) * 100).toFixed(2) : '0.00';
            const status = batsman.isOut ? 
                `Out (${batsman.outType}${batsman.fielder ? ` c ${batsman.fielder}` : ''})` :
                (batsman.striker ? 'Batting ✱' : 'Not out');
            
            tr.innerHTML = `
                <td>${player.name}</td>
                <td>${batsman.runs}</td>
                <td>${batsman.balls}</td>
                <td>${batsman.fours}</td>
                <td>${batsman.sixes}</td>
                <td>${strikeRate}</td>
                <td>${status}</td>
            `;
            
            tbody.appendChild(tr);
        });
    }
    
    updateBowlingStats() {
        const tbody = document.getElementById('bowlingStatsBody');
        tbody.innerHTML = '';
        
        this.bowlers.forEach(bowler => {
            const player = this.players.find(p => p.id === bowler.playerId);
            if (!player) return;
            
            const overs = Math.floor(bowler.balls / 6);
            const balls = bowler.balls % 6;
            const oversFormatted = `${overs}.${balls}`;
            
            const economy = (bowler.balls / 6) > 0 ? 
                (bowler.runs / (bowler.balls / 6)).toFixed(2) : '0.00';
            
            const tr = document.createElement('tr');
            if (bowler.isBowler) tr.classList.add('striker');
            
            tr.innerHTML = `
                <td>${player.name}</td>
                <td>${oversFormatted}</td>
                <td>${bowler.maidens}</td>
                <td>${bowler.runs}</td>
                <td>${bowler.wickets}</td>
                <td>${economy}</td>
                <td>${bowler.isBowler ? 'Bowling ✱' : ''}</td>
            `;
            
            tbody.appendChild(tr);
        });
    }
    
    updateOverDisplay() {
        const container = document.getElementById('currentOverBalls');
        container.innerHTML = '';
        
        for (let i = 0; i < 6; i++) {
            const ballDiv = document.createElement('div');
            ballDiv.className = 'ball-indicator ball-dot';
            
            if (i < this.currentOver.length) {
                const ball = this.currentOver[i];
                ballDiv.textContent = this.getBallSymbol(ball);
                ballDiv.className = `ball-indicator ball-${ball.type}`;
            }
            
            container.appendChild(ballDiv);
        }
        
        document.getElementById('overNumber').textContent = this.currentScore.overs + 1;
        document.getElementById('ballNumber').textContent = this.currentScore.balls;
    }
    
    getBallSymbol(ball) {
        switch(ball.type) {
            case 'run': return ball.value;
            case 'wicket': return 'W';
            case 'extra': 
                switch(ball.value) {
                    case 'wide': return 'WD';
                    case 'noBall': return 'NB';
                    case 'bye': return 'B';
                    case 'legBye': return 'LB';
                    default: return 'E';
                }
            default: return '•';
        }
    }
    
    updateRecentActions() {
        const log = document.getElementById('actionsLog');
        log.innerHTML = '';
        
        // Show last 10 actions
        const recentActions = this.actionHistory.slice(-10).reverse();
        
        recentActions.forEach(action => {
            const div = document.createElement('div');
            div.className = `action-item action-${action.type}`;
            
            div.innerHTML = `
                <span>${action.description}</span>
                <span class="action-time">${action.time}</span>
            `;
            
            log.appendChild(div);
        });
    }
    
    addActionToLog(type, description) {
        const now = new Date();
        const time = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
        
        this.actionHistory.push({
            type: type.toLowerCase(),
            description: description,
            time: time,
            timestamp: now.getTime()
        });
        
        this.updateRecentActions();
    }
    
    saveToLocalStorage() {
        const data = {
            players: this.players,
            matchOvers: this.matchOvers,
            targetRuns: this.targetRuns,
            currentScore: this.currentScore,
            batsmen: this.batsmen,
            bowlers: this.bowlers,
            currentOver: this.currentOver,
            actionHistory: this.actionHistory,
            currentStriker: this.currentStriker,
            currentNonStriker: this.currentNonStriker,
            currentBowler: this.currentBowler,
            nextBowler: this.nextBowler,
            isFirstInnings: this.isFirstInnings,
            matchStarted: this.matchStarted
        };
        
        localStorage.setItem('cricketScorecardData', JSON.stringify(data));
    }
    
    loadFromLocalStorage() {
        const saved = localStorage.getItem('cricketScorecardData');
        if (!saved) return;
        
        try {
            const data = JSON.parse(saved);
            
            this.players = data.players || [];
            this.matchOvers = data.matchOvers || 20;
            this.targetRuns = data.targetRuns || null;
            this.currentScore = data.currentScore || {
                runs: 0,
                wickets: 0,
                overs: 0,
                balls: 0,
                extras: { wide: 0, noBall: 0, bye: 0, legBye: 0 }
            };
            this.batsmen = data.batsmen || [];
            this.bowlers = data.bowlers || [];
            this.currentOver = data.currentOver || [];
            this.actionHistory = data.actionHistory || [];
            this.currentStriker = data.currentStriker || null;
            this.currentNonStriker = data.currentNonStriker || null;
            this.currentBowler = data.currentBowler || null;
            this.nextBowler = data.nextBowler || null;
            this.isFirstInnings = data.isFirstInnings !== undefined ? data.isFirstInnings : true;
            this.matchStarted = data.matchStarted || false;
            
            // Update UI if match was in progress
            if (this.matchStarted) {
                this.renderPlayerList();
                this.updateScoreboard();
                this.hideSetupModal();
                
                // Update selects
                if (this.currentStriker) {
                    document.getElementById('strikerSelect').value = this.currentStriker;
                }
                if (this.currentNonStriker) {
                    document.getElementById('nonStrikerSelect').value = this.currentNonStriker;
                }
                if (this.currentBowler) {
                    document.getElementById('currentBowlerSelect').value = this.currentBowler;
                    const bowler = this.players.find(p => p.id === this.currentBowler);
                    if (bowler) {
                        document.getElementById('currentBowlerName').textContent = bowler.name;
                    }
                }
                if (this.nextBowler) {
                    document.getElementById('nextBowlerSelect').value = this.nextBowler;
                }
            }
            
        } catch (e) {
            console.error('Error loading saved data:', e);
        }
    }
}

// Initialize the app when page loads
window.addEventListener('DOMContentLoaded', () => {
    const app = new CricketScorecard();
    
    // Make app globally available for debugging
    window.cricketApp = app;
});
