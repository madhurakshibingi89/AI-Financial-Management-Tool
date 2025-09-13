<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Financial Management Tool</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.9.1/chart.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.21/lodash.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --primary: #667eea;
            --primary-dark: #5a6fd8;
            --secondary: #764ba2;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
            --bg-primary: #0f0f23;
            --bg-secondary: #1a1a2e;
            --bg-card: #16213e;
            --text-primary: #ffffff;
            --text-secondary: #a0aec0;
            --border: #2d3748;
            --gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: var(--bg-primary);
            color: var(--text-primary);
            line-height: 1.6;
        }

        .app-container {
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }

        .header {
            background: var(--gradient);
            padding: 1rem 2rem;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
        }

        .header h1 {
            font-size: 2rem;
            font-weight: 700;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        .nav-tabs {
            display: flex;
            gap: 1rem;
            margin-top: 1rem;
            border-bottom: 2px solid rgba(255, 255, 255, 0.1);
        }

        .nav-tab {
            padding: 0.75rem 1.5rem;
            background: none;
            border: none;
            color: rgba(255, 255, 255, 0.7);
            cursor: pointer;
            border-bottom: 2px solid transparent;
            transition: all 0.3s ease;
            font-weight: 500;
        }

        .nav-tab.active,
        .nav-tab:hover {
            color: white;
            border-bottom-color: white;
        }

        .main-content {
            flex: 1;
            padding: 2rem;
            max-width: 1400px;
            margin: 0 auto;
            width: 100%;
        }

        .tab-content {
            display: none;
        }

        .tab-content.active {
            display: block;
            animation: fadeIn 0.3s ease;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .dashboard-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 2rem;
            margin-bottom: 2rem;
        }

        .card {
            background: var(--bg-card);
            border-radius: 12px;
            padding: 1.5rem;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
            border: 1px solid var(--border);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .card:hover {
            transform: translateY(-4px);
            box-shadow: 0 12px 40px rgba(102, 126, 234, 0.2);
        }

        .card h3 {
            margin-bottom: 1rem;
            color: var(--primary);
            font-size: 1.2rem;
        }

        .metric-card {
            text-align: center;
            background: linear-gradient(135deg, var(--bg-card) 0%, rgba(102, 126, 234, 0.1) 100%);
        }

        .metric-value {
            font-size: 2.5rem;
            font-weight: bold;
            margin-bottom: 0.5rem;
        }

        .metric-label {
            color: var(--text-secondary);
            font-size: 0.9rem;
        }

        .form-group {
            margin-bottom: 1rem;
        }

        .form-group label {
            display: block;
            margin-bottom: 0.5rem;
            color: var(--text-primary);
            font-weight: 500;
        }

        .form-control {
            width: 100%;
            padding: 0.75rem;
            border: 1px solid var(--border);
            border-radius: 8px;
            background: var(--bg-secondary);
            color: var(--text-primary);
            font-size: 1rem;
            transition: border-color 0.3s ease;
        }

        .form-control:focus {
            outline: none;
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        .btn {
            padding: 0.75rem 1.5rem;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1rem;
            font-weight: 500;
            transition: all 0.3s ease;
            text-decoration: none;
            display: inline-block;
        }

        .btn-primary {
            background: var(--gradient);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 20px rgba(102, 126, 234, 0.4);
        }

        .btn-success {
            background: var(--success);
            color: white;
        }

        .btn-warning {
            background: var(--warning);
            color: white;
        }

        .btn-danger {
            background: var(--danger);
            color: white;
        }

        .expense-list {
            max-height: 400px;
            overflow-y: auto;
        }

        .expense-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1rem;
            border-bottom: 1px solid var(--border);
            transition: background-color 0.3s ease;
        }

        .expense-item:hover {
            background: rgba(102, 126, 234, 0.1);
        }

        .expense-category {
            display: inline-block;
            padding: 0.25rem 0.75rem;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: 500;
            margin-left: 0.5rem;
        }

        .category-food { background: rgba(244, 63, 94, 0.2); color: #f472b6; }
        .category-transport { background: rgba(59, 130, 246, 0.2); color: #60a5fa; }
        .category-entertainment { background: rgba(168, 85, 247, 0.2); color: #c084fc; }
        .category-utilities { background: rgba(34, 197, 94, 0.2); color: #4ade80; }
        .category-shopping { background: rgba(251, 191, 36, 0.2); color: #fbbf24; }
        .category-healthcare { background: rgba(239, 68, 68, 0.2); color: #f87171; }
        .category-other { background: rgba(156, 163, 175, 0.2); color: #9ca3af; }

        .goal-progress {
            background: var(--bg-secondary);
            border-radius: 10px;
            padding: 0.5rem;
            margin-top: 1rem;
        }

        .progress-bar {
            height: 8px;
            background: var(--bg-primary);
            border-radius: 4px;
            overflow: hidden;
            position: relative;
        }

        .progress-fill {
            height: 100%;
            background: var(--gradient);
            border-radius: 4px;
            transition: width 0.3s ease;
        }

        .alert {
            padding: 1rem;
            border-radius: 8px;
            margin-bottom: 1rem;
            border-left: 4px solid;
        }

        .alert-success {
            background: rgba(16, 185, 129, 0.1);
            border-color: var(--success);
            color: var(--success);
        }

        .alert-warning {
            background: rgba(245, 158, 11, 0.1);
            border-color: var(--warning);
            color: var(--warning);
        }

        .alert-danger {
            background: rgba(239, 68, 68, 0.1);
            border-color: var(--danger);
            color: var(--danger);
        }

        .chart-container {
            position: relative;
            height: 300px;
            margin-top: 1rem;
        }

        .predictions-list {
            list-style: none;
        }

        .predictions-list li {
            padding: 0.75rem;
            margin-bottom: 0.5rem;
            background: var(--bg-secondary);
            border-radius: 8px;
            border-left: 4px solid var(--primary);
        }

        .two-column {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 2rem;
        }

        @media (max-width: 768px) {
            .two-column {
                grid-template-columns: 1fr;
            }
            
            .dashboard-grid {
                grid-template-columns: 1fr;
            }
            
            .main-content {
                padding: 1rem;
            }
        }
    </style>
</head>
<body>
    <div class="app-container">
        <header class="header">
            <h1>🤖 AI Financial Management Tool</h1>
            <nav class="nav-tabs">
                <button class="nav-tab active" data-tab="dashboard">Dashboard</button>
                <button class="nav-tab" data-tab="expenses">Expenses</button>
                <button class="nav-tab" data-tab="budget">Budget</button>
                <button class="nav-tab" data-tab="goals">Goals</button>
                <button class="nav-tab" data-tab="analytics">Analytics</button>
            </nav>
        </header>

        <main class="main-content">
            <!-- Dashboard Tab -->
            <div id="dashboard" class="tab-content active">
                <div class="dashboard-grid">
                    <div class="card metric-card">
                        <div class="metric-value" id="totalBalance">$0</div>
                        <div class="metric-label">Total Balance</div>
                    </div>
                    <div class="card metric-card">
                        <div class="metric-value" id="monthlySpending">$0</div>
                        <div class="metric-label">Monthly Spending</div>
                    </div>
                    <div class="card metric-card">
                        <div class="metric-value" id="budgetUsed">0%</div>
                        <div class="metric-label">Budget Used</div>
                    </div>
                    <div class="card metric-card">
                        <div class="metric-value" id="goalsProgress">0%</div>
                        <div class="metric-label">Goals Progress</div>
                    </div>
                </div>

                <div class="two-column">
                    <div class="card">
                        <h3>📊 Spending by Category</h3>
                        <div class="chart-container">
                            <canvas id="categoryChart"></canvas>
                        </div>
                    </div>
                    <div class="card">
                        <h3>🚨 Budget Alerts</h3>
                        <div id="budgetAlerts"></div>
                    </div>
                </div>
            </div>

            <!-- Expenses Tab -->
            <div id="expenses" class="tab-content">
                <div class="two-column">
                    <div class="card">
                        <h3>➕ Add New Expense</h3>
                        <form id="expenseForm">
                            <div class="form-group">
                                <label for="expenseDescription">Description</label>
                                <input type="text" id="expenseDescription" class="form-control" placeholder="e.g., Lunch at restaurant" required>
                            </div>
                            <div class="form-group">
                                <label for="expenseAmount">Amount ($)</label>
                                <input type="number" id="expenseAmount" class="form-control" step="0.01" required>
                            </div>
                            <div class="form-group">
                                <label for="expenseDate">Date</label>
                                <input type="date" id="expenseDate" class="form-control" required>
                            </div>
                            <button type="submit" class="btn btn-primary">Add Expense (AI will categorize)</button>
                        </form>
                    </div>

                    <div class="card">
                        <h3>📱 Recent Expenses</h3>
                        <div class="expense-list" id="expenseList"></div>
                    </div>
                </div>
            </div>

            <!-- Budget Tab -->
            <div id="budget" class="tab-content">
                <div class="two-column">
                    <div class="card">
                        <h3>💰 Set Monthly Budgets</h3>
                        <form id="budgetForm">
                            <div class="form-group">
                                <label for="budgetCategory">Category</label>
                                <select id="budgetCategory" class="form-control" required>
                                    <option value="">Select Category</option>
                                    <option value="food">Food & Dining</option>
                                    <option value="transport">Transportation</option>
                                    <option value="entertainment">Entertainment</option>
                                    <option value="utilities">Utilities</option>
                                    <option value="shopping">Shopping</option>
                                    <option value="healthcare">Healthcare</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <label for="budgetAmount">Monthly Budget ($)</label>
                                <input type="number" id="budgetAmount" class="form-control" step="0.01" required>
                            </div>
                            <button type="submit" class="btn btn-primary">Set Budget</button>
                        </form>
                    </div>

                    <div class="card">
                        <h3>📈 Budget Overview</h3>
                        <div id="budgetOverview"></div>
                    </div>
                </div>
            </div>

            <!-- Goals Tab -->
            <div id="goals" class="tab-content">
                <div class="two-column">
                    <div class="card">
                        <h3>🎯 Create Financial Goal</h3>
                        <form id="goalForm">
                            <div class="form-group">
                                <label for="goalName">Goal Name</label>
                                <input type="text" id="goalName" class="form-control" placeholder="e.g., Emergency Fund" required>
                            </div>
                            <div class="form-group">
                                <label for="goalTarget">Target Amount ($)</label>
                                <input type="number" id="goalTarget" class="form-control" step="0.01" required>
                            </div>
                            <div class="form-group">
                                <label for="goalDeadline">Target Date</label>
                                <input type="date" id="goalDeadline" class="form-control" required>
                            </div>
                            <div class="form-group">
                                <label for="goalCurrent">Current Amount ($)</label>
                                <input type="number" id="goalCurrent" class="form-control" step="0.01" value="0">
                            </div>
                            <button type="submit" class="btn btn-success">Create Goal</button>
                        </form>
                    </div>

                    <div class="card">
                        <h3>🏆 Your Goals</h3>
                        <div id="goalsList"></div>
                    </div>
                </div>
            </div>

            <!-- Analytics Tab -->
            <div id="analytics" class="tab-content">
                <div class="card">
                    <h3>🔮 AI Predictive Analytics</h3>
                    <div class="chart-container">
                        <canvas id="predictiveChart"></canvas>
                    </div>
                </div>

                <div class="two-column">
                    <div class="card">
                        <h3>📊 Spending Trends</h3>
                        <div class="chart-container">
                            <canvas id="trendsChart"></canvas>
                        </div>
                    </div>
                    <div class="card">
                        <h3>🤖 AI Insights</h3>
                        <ul class="predictions-list" id="aiInsights"></ul>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <script>
        // Financial Data Management
        class FinancialManager {
            constructor() {
                this.expenses = this.loadData('expenses', []);
                this.budgets = this.loadData('budgets', {});
                this.goals = this.loadData('goals', []);
                this.balance = this.loadData('balance', 5000);
                this.initializeCharts();
                this.updateDashboard();
            }

            loadData(key, defaultValue) {
                const stored = JSON.parse(window.localStorage?.getItem(key) || 'null');
                return stored !== null ? stored : defaultValue;
            }

            saveData(key, data) {
                try {
                    window.localStorage?.setItem(key, JSON.stringify(data));
                } catch (e) {
                    console.log('Using in-memory storage');
                }
            }

            // AI-powered expense categorization
            categorizeExpense(description) {
                const keywords = {
                    food: ['restaurant', 'lunch', 'dinner', 'coffee', 'food', 'grocery', 'pizza', 'burger', 'starbucks', 'mcdonald'],
                    transport: ['uber', 'taxi', 'gas', 'fuel', 'parking', 'metro', 'bus', 'train', 'flight'],
                    entertainment: ['movie', 'cinema', 'game', 'spotify', 'netflix', 'concert', 'theater'],
                    utilities: ['electricity', 'water', 'internet', 'phone', 'cable', 'utility'],
                    shopping: ['amazon', 'mall', 'clothes', 'shopping', 'store', 'purchase'],
                    healthcare: ['doctor', 'hospital', 'pharmacy', 'medicine', 'health', 'dental']
                };

                const desc = description.toLowerCase();
                
                for (const [category, words] of Object.entries(keywords)) {
                    if (words.some(word => desc.includes(word))) {
                        return category;
                    }
                }
                
                return 'other';
            }

            addExpense(description, amount, date) {
                const category = this.categorizeExpense(description);
                const expense = {
                    id: Date.now(),
                    description,
                    amount: parseFloat(amount),
                    date,
                    category
                };

                this.expenses.unshift(expense);
                this.balance -= expense.amount;
                this.saveData('expenses', this.expenses);
                this.saveData('balance', this.balance);
                this.updateDashboard();
                this.checkBudgetAlerts();
            }

            setBudget(category, amount) {
                this.budgets[category] = parseFloat(amount);
                this.saveData('budgets', this.budgets);
                this.updateDashboard();
            }

            addGoal(name, target, deadline, current = 0) {
                const goal = {
                    id: Date.now(),
                    name,
                    target: parseFloat(target),
                    current: parseFloat(current),
                    deadline,
                    created: new Date().toISOString()
                };

                this.goals.push(goal);
                this.saveData('goals', this.goals);
                this.updateDashboard();
            }

            updateGoal(id, current) {
                const goal = this.goals.find(g => g.id === id);
                if (goal) {
                    goal.current = parseFloat(current);
                    this.saveData('goals', this.goals);
                    this.updateDashboard();
                }
            }

            // Predictive Analytics
            predictSpending() {
                if (this.expenses.length < 3) return [];

                const monthlyData = this.getMonthlySpending();
                const trend = this.calculateTrend(monthlyData);
                const predictions = [];

                for (let i = 1; i <= 3; i++) {
                    const predictedAmount = monthlyData[monthlyData.length - 1] + (trend * i);
                    predictions.push({
                        month: this.getNextMonth(i),
                        amount: Math.max(0, predictedAmount)
                    });
                }

                return predictions;
            }

            getMonthlySpending() {
                const monthlyTotals = {};
                this.expenses.forEach(expense => {
                    const month = expense.date.substring(0, 7); // YYYY-MM
                    monthlyTotals[month] = (monthlyTotals[month] || 0) + expense.amount;
                });

                return Object.values(monthlyTotals).slice(-6); // Last 6 months
            }

            calculateTrend(data) {
                if (data.length < 2) return 0;
                
                const n = data.length;
                const sumX = data.reduce((sum, _, i) => sum + i, 0);
                const sumY = data.reduce((sum, val) => sum + val, 0);
                const sumXY = data.reduce((sum, val, i) => sum + (i * val), 0);
                const sumXX = data.reduce((sum, _, i) => sum + (i * i), 0);

                return (n * sumXY - sumX * sumY) / (n * sumXX - sumX * sumX);
            }

            getNextMonth(offset) {
                const date = new Date();
                date.setMonth(date.getMonth() + offset);
                return date.toLocaleDateString('en-US', { month: 'short', year: 'numeric' });
            }

            generateInsights() {
                const insights = [];
                const currentMonth = new Date().toISOString().substring(0, 7);
                const monthlySpending = this.expenses
                    .filter(e => e.date.startsWith(currentMonth))
                    .reduce((sum, e) => sum + e.amount, 0);

                const categorySpending = this.getCategorySpending();
                const topCategory = Object.entries(categorySpending)
                    .sort(([,a], [,b]) => b - a)[0];

                if (monthlySpending > 2000) {
                    insights.push("⚠️ Your spending is higher than average this month. Consider reviewing your expenses.");
                }

                if (topCategory) {
                    insights.push(`💡 You spend most on ${topCategory[0]} ($${topCategory[1].toFixed(2)}). Look for savings opportunities here.`);
                }

                const predictions = this.predictSpending();
                if (predictions.length > 0) {
                    insights.push(`📈 Based on trends, you're predicted to spend $${predictions[0].amount.toFixed(2)} next month.`);
                }

                const unachievableGoals = this.goals.filter(goal => {
                    const daysLeft = Math.max(0, (new Date(goal.deadline) - new Date()) / (1000 * 60 * 60 * 24));
                    const needed = goal.target - goal.current;
                    const dailyRequired = needed / daysLeft;
                    return dailyRequired > 50; // Arbitrary threshold
                });

                if (unachievableGoals.length > 0) {
                    insights.push("🎯 Some goals may need deadline adjustments based on current savings rate.");
                }

                return insights;
            }

            checkBudgetAlerts() {
                const currentMonth = new Date().toISOString().substring(0, 7);
                const monthlySpending = {};

                this.expenses
                    .filter(e => e.date.startsWith(currentMonth))
                    .forEach(expense => {
                        monthlySpending[expense.category] = (monthlySpending[expense.category] || 0) + expense.amount;
                    });

                const alerts = [];
                Object.entries(this.budgets).forEach(([category, budget]) => {
                    const spent = monthlySpending[category] || 0;
                    const percentage = (spent / budget) * 100;

                    if (percentage >= 100) {
                        alerts.push({ category, type: 'danger', message: `Budget exceeded by $${(spent - budget).toFixed(2)}!` });
                    } else if (percentage >= 80) {
                        alerts.push({ category, type: 'warning', message: `${percentage.toFixed(0)}% of budget used` });
                    }
                });

                this.displayBudgetAlerts(alerts);
            }

            getCategorySpending() {
                const currentMonth = new Date().toISOString().substring(0, 7);
                const categoryTotals = {};

                this.expenses
                    .filter(e => e.date.startsWith(currentMonth))
                    .forEach(expense => {
                        categoryTotals[expense.category] = (categoryTotals[expense.category] || 0) + expense.amount;
                    });

                return categoryTotals;
            }

            // UI Updates
            updateDashboard() {
                this.updateMetrics();
                this.updateExpensesList();
                this.updateBudgetOverview();
                this.updateGoalsList();
                this.updateCharts();
            }

            updateMetrics() {
                const currentMonth = new Date().toISOString().substring(0, 7);
                const monthlySpending = this.expenses
                    .filter(e => e.date.startsWith(currentMonth))
                    .reduce((sum, e) => sum + e.amount, 0);

                const totalBudget = Object.values(this.budgets).reduce((sum, b) => sum + b, 0);
                const budgetUsed = totalBudget > 0 ? (monthlySpending / totalBudget) * 100 : 0;

                const totalGoalsProgress = this.goals.length > 0 
                    ? this.goals.reduce((sum, g) => sum + (g.current / g.target), 0) / this.goals.length * 100
                    : 0;

                document.getElementById('totalBalance').textContent = `$${this.balance.toFixed(2)}`;
                document.getElementById('monthlySpending').textContent = `$${monthlySpending.toFixed(2)}`;
                document.getElementById('budgetUsed').textContent = `${budgetUsed.toFixed(0)}%`;
                document.getElementById('goalsProgress').textContent = `${totalGoalsProgress.toFixed(0)}%`;
            }

            updateExpensesList() {
                const container = document.getElementById('expenseList');
                container.innerHTML = this.expenses.slice(0, 10).map(expense => `
                    <div class="expense-item">
                        <div>
                            <strong>${expense.description}</strong>
                            <span class="expense-category category-${expense.category}">${expense.category}</span>
                            <div style="font-size: 0.8rem; color: var(--text-secondary);">${expense.date}</div>
                        </div>
                        <div style="font-weight: bold; color: var(--danger);">-$${expense.amount.toFixed(2)}</div>
                    </div>
                `).join('');
            }

            updateBudgetOverview() {
                const container = document.getElementById('budgetOverview');
                const currentMonth = new Date().toISOString().substring(0, 7);
                const categorySpending = this.getCategorySpending();

                container.innerHTML = Object.entries(this.budgets).map(([category, budget]) => {
                    const spent = categorySpending[category] || 0;
                    const percentage = Math.min((spent / budget) * 100, 100);
                    
                    return `
                        <div style="margin-bottom: 1rem;">
                            <div style="display: flex; justify-content: space-between; margin-bottom: 0.5rem;">
                                <span style="text-transform: capitalize;">${category}</span>
                                <span>$${spent.toFixed(2)} / $${budget.toFixed(2)}</span>
                            </div>
                            <div class="progress-bar">
                                <div class="progress-fill" style="width: ${percentage}%;"></div>
                            </div>
                        </div>
                    `;
                }).join('') || '<p style="color: var(--text-secondary);">No budgets set yet.</p>';
            }

            updateGoalsList() {
                const container = document.getElementById('goalsList');
                container.innerHTML = this.goals.map(goal => {
                    const progress = Math.min((goal.current / goal.target) * 100, 100);
                    const daysLeft = Math.max(0, Math.ceil((new Date(goal.deadline) - new Date()) / (1000 * 60 * 60 * 24)));
                    
                    return `
                        <div class="goal-progress">
                            <div style="display: flex; justify-content: space-between; margin-bottom: 0.5rem;">
                                <strong>${goal.name}</strong>
                                <span>${daysLeft} days left</span>
                            </div>
                            <div style="display: flex;
