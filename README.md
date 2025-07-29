<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quizify</title>
    
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #6e8efb, #a777e3);
            color: #000;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 1.5rem;
        }
        h1 {
            font-size: 2.5rem;
            color: #fff;
            text-shadow: 0 0.1rem 0.3rem rgba(0, 0, 0, 0.3);
            margin-bottom: 2rem;
            text-align: center;
            animation: fadeIn 0.5s ease-in;
        }
        .quiz-container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 1.5rem;
            padding: 2.5rem;
            max-width: 90vw;
            width: 700px;
            box-shadow: 0 0.5rem 1.5rem rgba(0, 0, 0, 0.2);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        .quiz-container:hover {
            transform: scale(1.03);
            box-shadow: 0 0.75rem 2rem rgba(0, 0, 0, 0.3);
        }
        .question {
            font-size: 1.8rem;
            margin-bottom: 2rem;
            color: #000;
            text-shadow: 0 0.1rem 0.3rem rgba(0, 0, 0, 0.2);
            animation: fadeIn 0.5s ease-in;
        }
        .options {
            display: flex;
            flex-direction: column;
            gap: 1.2rem;
        }
        .option {
            padding: 1rem 1.5rem;
            background: rgba(255, 255, 255, 0.2);
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 0.75rem;
            cursor: pointer;
            font-size: 1.2rem;
            color: #000;
            text-align: left;
            transition: all 0.4s ease;
            position: relative;
            overflow: hidden;
        }
        .option::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            width: 0;
            height: 0;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 50%;
            transform: translate(-50%, -50%);
            transition: width 0.6s ease, height 0.6s ease;
        }
        .option:hover::before {
            width: 300px;
            height: 300px;
        }
        .option:hover {
            background: rgba(255, 255, 255, 0.3);
            border-color: rgba(255, 255, 255, 0.5);
            transform: translateY(-0.2rem);
        }
        .option.correct {
            background: rgba(46, 204, 113, 0.7);
            border-color: rgba(39, 174, 96, 0.8);
            color: #000;
        }
        .option.wrong {
            background: rgba(231, 76, 60, 0.7);
            border-color: rgba(192, 57, 43, 0.8);
            color: #000;
        }
        #result {
            margin-top: 2rem;
            font-size: 1.4rem;
            font-weight: 600;
            color: #000;
            text-shadow: 0 0.1rem 0.3rem rgba(0, 0, 0, 0.2);
            animation: slideUp 0.5s ease-out;
        }
        #result.correct {
            color: #000;
        }
        .restart-btn {
            margin-top: 1.5rem;
            padding: 0.75rem 1.5rem;
            font-size: 1.1rem;
            color: #000;
            background: rgba(255, 255, 255, 0.3);
            border: 2px solid rgba(255, 255, 255, 0.5);
            border-radius: 0.5rem;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        .restart-btn:hover {
            background: rgba(255, 255, 255, 0.5);
            transform: translateY(-0.1rem);
        }
        /* Animations */
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        @keyframes slideUp {
            from { transform: translateY(20px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        /* Media Queries for Different Screen Types */
        @media (max-width: 1024px) {
            h1 {
                font-size: 2.2rem;
            }
            .quiz-container {
                padding: 2rem;
            }
            .question {
                font-size: 1.5rem;
            }
            .option {
                font-size: 1.1rem;
            }
        }
        @media (max-width: 768px) {
            h1 {
                font-size: 1.8rem;
            }
            .quiz-container {
                padding: 1.5rem;
            }
            .question {
                font-size: 1.3rem;
            }
            .option {
                font-size: 1rem;
            }
        }
        @media (max-width: 480px) {
            h1 {
                font-size: 1.5rem;
                margin-bottom: 1.5rem;
            }
            .quiz-container {
                padding: 1rem;
            }
            .question {
                font-size: 1.1rem;
            }
            .option {
                font-size: 0.95rem;
                padding: 0.75rem 1rem;
            }
            #result {
                font-size: 1.2rem;
            }
            .restart-btn {
                font-size: 1rem;
                padding: 0.6rem 1.2rem;
            }
        }
        @media (max-height: 600px) {
            h1 {
                font-size: 1.8rem;
                margin-bottom: 1rem;
            }
            .quiz-container {
                padding: 1rem;
            }
            .question {
                margin-bottom: 1rem;
            }
            .options {
                gap: 0.8rem;
            }
        }
    </style>
</head>
<body>
    <h1>Welcome to Quizify Challenge</h1>
    <div class="quiz-container">
        <div class="question" id="question"></div>
        <div class="options" id="options"></div>
        <div id="result"></div>
        <button class="restart-btn" id="restartBtn" style="display: none;">Restart Quiz</button>
    </div>

    <script>
        const quizData = [
            {
                question: "What is the capital of France?",
                options: ["Berlin", "Madrid", "Paris", "Rome"],
                answer: "Paris"
            },
            {
                question: "Which planet is known as the Red Planet?",
                options: ["Venus", "Mars", "Jupiter", "Saturn"],
                answer: "Mars"
            },
            {
                question: "What is the capital of France?",
                options: ["Berlin", "Madrid", "Paris", "Rome"],
                answer: "Paris"
            },
            {
                question: "Which planet is known as the Red Planet?",
                options: ["Venus", "Mars", "Jupiter", "Saturn"],
                answer: "Mars"
            },
            {
                question: "Which country is known as the Land of the Rising Sun?",
                options: ["China","South Korea","Japan","Thailand"],
                answer: "Japan"
            },
            {
                question: "Who is the founder of Microsoft?",
                options: [" Steve Jobs","Elon Musk","Bill Gates","Mark Zuckerberg"],
                answer: "Bill Gates"
            },
            {
                question: "What is the square root of 144?",
                options: [" 11","12","13","14"],
                answer: "12"
            },
            {
                question: "Who is the Prime Minister of India",
                options: ["Manmohan Singh","Narendra Modi","Rahul Gandhi","Arvind Kejriwal"],
                answer: "Narendra Modi"
            },
            {
                question: "What is the chemical name of Gold",
                options: ["Au","Gu","Tu","Pu"],
                answer: "Au"
            },
        ];

        let currentQuestion = 0;
        const questionElement = document.getElementById('question');
        const optionsElement = document.getElementById('options');
        const resultElement = document.getElementById('result');
        const restartBtn = document.getElementById('restartBtn');

        function loadQuestion() {
            const currentQuiz = quizData[currentQuestion];
            questionElement.textContent = currentQuiz.question;
            optionsElement.innerHTML = '';
            currentQuiz.options.forEach(option => {
                const div = document.createElement('div');
                div.className = 'option';
                div.textContent = option;
                div.addEventListener('click', () => checkAnswer(option, div));
                optionsElement.appendChild(div);
            });
            restartBtn.style.display = 'none';
        }

        function checkAnswer(selected, element) {
            const currentQuiz = quizData[currentQuestion];
            const options = optionsElement.getElementsByClassName('option');
            for (let opt of options) {
                opt.style.pointerEvents = 'none';
                if (opt.textContent === currentQuiz.answer) {
                    opt.classList.add('correct');
                } else if (opt.textContent === selected && selected !== currentQuiz.answer) {
                    opt.classList.add('wrong');
                }
            }
            if (selected === currentQuiz.answer) {
                resultElement.textContent = "Correct!";
                resultElement.classList.add('correct');
            } else {
                resultElement.textContent = `Wrong! The correct answer is ${currentQuiz.answer}.`;
            }
            setTimeout(nextQuestion, 1500);
        }

        function nextQuestion() {
            currentQuestion++;
            resultElement.classList.remove('correct');
            if (currentQuestion < quizData.length) {
                resultElement.textContent = '';
                loadQuestion();
            } else {
                resultElement.textContent = "Quiz completed!";
                restartBtn.style.display = 'block';
            }
        }

        restartBtn.addEventListener('click', () => {
            location.reload();
        });

        loadQuestion();
    </script>
</body>
</html>
