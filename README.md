Gunajit-Calculas-solver
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculus Calculator</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/12.4.1/math.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Roboto', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            color: white;
            padding: 30px;
            text-align: center;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }

        .main-content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            padding: 40px;
        }

        .input-section, .result-section {
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        }

        .input-group {
            margin-bottom: 25px;
        }

        label {
            display: block;
            margin-bottom: 10px;
            font-weight: 500;
            color: #333;
            font-size: 1.1em;
        }

        input[type="text"], select {
            width: 100%;
            padding: 15px;
            border: 2px solid #e1e5e9;
            border-radius: 10px;
            font-size: 1.1em;
            transition: all 0.3s ease;
        }

        input[type="text"]:focus, select:focus {
            outline: none;
            border-color: #4facfe;
            box-shadow: 0 0 0 3px rgba(79, 172, 254, 0.1);
        }

        .limits-group {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }

        .btn {
            width: 100%;
            padding: 15px;
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 1.1em;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s ease;
            margin: 10px 0;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 25px rgba(79, 172, 254, 0.4);
        }

        .btn:active {
            transform: translateY(0);
        }

        .result {
            min-height: 200px;
            padding: 20px;
            background: #f8f9fa;
            border-radius: 10px;
            border-left: 5px solid #4facfe;
        }

        .result-item {
            margin-bottom: 20px;
            padding: 15px;
            background: white;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }

        .result-title {
            font-weight: 600;
            color: #333;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
        }

        .result-math {
            font-size: 1.3em;
            color: #4facfe;
            min-height: 40px;
        }

        .error {
            color: #e74c3c;
            background: #fdf2f2;
            border-left-color: #e74c3c;
        }

        @media (max-width: 768px) {
            .main-content {
                grid-template-columns: 1fr;
                padding: 20px;
            }
            
            .header h1 {
                font-size: 2em;
            }
        }

        .loading {
            text-align: center;
            color: #666;
            font-style: italic;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🧮 Calculus Calculator</h1>
            <p>Solve Differentiation & Integration with Limits</p>
        </div>
        
        <div class="main-content">
            <div class="input-section">
                <div class="input-group">
                    <label for="function">Enter Function (use x as variable):</label>
                    <input type="text" id="function" placeholder="e.g., x^2 + 3*x + sin(x)" value="x^2 + 3*x">
                </div>
                
                <div class="input-group">
                    <label>Operation:</label>
                    <select id="operation">
                        <option value="derivative">Differentiate (d/dx)</option>
                        <option value="integral">Indefinite Integral (∫)</option>
                        <option value="definite">Definite Integral (with limits)</option>
                    </select>
                </div>
                
                <div class="limits-group" id="limitsGroup" style="display: none;">
                    <div>
                        <label for="lowerLimit">Lower Limit:</label>
                        <input type="text" id="lowerLimit" placeholder="e.g., 0">
                    </div>
                    <div>
                        <label for="upperLimit">Upper Limit:</label>
                        <input type="text" id="upperLimit" placeholder="e.g., 2">
                    </div>
                </div>
                
                <button class="btn" onclick="calculate()">Calculate</button>
                <button class="btn" onclick="clearAll()">Clear All</button>
            </div>
            
            <div class="result-section">
                <div id="results">
                    <div class="loading">Enter a function and click Calculate</div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Toggle limits input based on operation
        document.getElementById('operation').addEventListener('change', function() {
            const limitsGroup = document.getElementById('limitsGroup');
            if (this.value === 'definite') {
                limitsGroup.style.display = 'grid';
            } else {
                limitsGroup.style.display = 'none';
            }
        });

        function calculate() {
            const func = document.getElementById('function').value;
            const operation = document.getElementById('operation').value;
            const resultsDiv = document.getElementById('results');
            
            if (!func.trim()) {
                showError('Please enter a function');
                return;
            }

            resultsDiv.innerHTML = '<div class="loading">Calculating...</div>';

            try {
                let result = '';
                
                if (operation === 'derivative') {
                    result = computeDerivative(func);
                } else if (operation === 'integral') {
                    result = computeIndefiniteIntegral(func);
                } else if (operation === 'definite') {
                    const lower = document.getElementById('lowerLimit').value;
                    const upper = document.getElementById('upperLimit').value;
                    if (!lower || !upper) {
                        showError('Please enter both limits for definite integral');
                        return;
                    }
                    result = computeDefiniteIntegral(func, lower, upper);
                }
                
                displayResult(operation, func, result);
                
            } catch (error) {
                showError('Error: ' + error.message);
            }
        }

        function computeDerivative(func) {
            const node = math.parse(func);
            const derivative = node.derivative('x');
            return derivative.toTex();
        }

        function computeIndefiniteIntegral(func) {
            const node = math.parse(func);
            const integral = node.integral('x');
            return integral.toTex();
        }

        function computeDefiniteIntegral(func, lower, upper) {
            // First
