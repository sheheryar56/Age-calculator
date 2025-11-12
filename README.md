# Age-calculator
Ye age calculator ap ki age k tamam months,weeks,days,mintor second btae ga
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Accurate Age Calculator</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles for better aesthetics */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f9fb;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .container-card {
            max-width: 500px;
            width: 95%;
            background: white;
            padding: 2.5rem 1.5rem;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
            border-radius: 1.5rem;
            transition: all 0.3s ease;
        }
        input[type="date"] {
            appearance: none;
            -webkit-appearance: none;
            width: 100%;
            padding: 0.75rem;
            border: 2px solid #e0e7ff;
            border-radius: 0.5rem;
            font-size: 1rem;
            color: #1f2937;
            background-color: #f9fafb;
            cursor: pointer;
            transition: border-color 0.2s;
        }
        input[type="date"]:focus {
            outline: none;
            border-color: #4f46e5;
            box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.2);
        }
        #calculateBtn {
            background-color: #4f46e5;
            color: white;
            transition: background-color 0.2s, transform 0.1s;
        }
        #calculateBtn:hover {
            background-color: #4338ca;
        }
        #calculateBtn:active {
            transform: scale(0.99);
        }
        .result-box {
            background-color: #eef2ff;
            border: 1px solid #c7d2fe;
        }
        .total-metric {
            padding: 0.5rem;
            border-radius: 0.75rem;
            flex-grow: 1;
            min-width: 45%;
        }
        .total-metric p:first-child {
            line-height: 1.2;
        }
    </style>
</head>
<body>

    <div class="container-card">
        <h1 class="text-3xl font-extrabold text-gray-900 mb-6 text-center">Ultimate Age Calculator</h1>
        <p class="text-gray-500 mb-8 text-center">Enter your date of birth to find your exact age and total time lived.</p>

        <div class="mb-6">
            <label for="dobInput" class="block text-sm font-medium text-gray-700 mb-2">Date of Birth</label>
            <input type="date" id="dobInput" class="dob-input" max="">
        </div>

        <button id="calculateBtn" class="w-full py-3 rounded-xl font-semibold shadow-lg hover:shadow-xl focus:outline-none focus:ring-4 focus:ring-indigo-500 focus:ring-opacity-50">
            Calculate Age
        </button>

        <div id="resultOutput" class="result-box mt-8 p-6 rounded-xl hidden">
            <!-- Exact Age Section -->
            <div class="text-center pb-4 mb-4 border-b border-indigo-200">
                <p class="text-base text-indigo-800 font-bold mb-2 uppercase tracking-wider">Exact Calendar Age</p>
                <div id="ageDisplay" class="text-2xl font-bold text-gray-900 leading-tight"></div>
                <p id="nextBirthday" class="text-sm text-indigo-600 mt-3 font-medium"></p>
            </div>

            <!-- Total Time Lived Section -->
            <div class="text-center pt-2">
                <p class="text-base text-indigo-800 font-bold mb-4 uppercase tracking-wider">Total Time Lived</p>
                <div id="totalAgeDisplay" class="flex flex-wrap justify-center gap-3">
                    <!-- Metrics will be inserted here by JavaScript -->
                </div>
            </div>
        </div>

        <div id="messageBox" class="mt-4 p-3 rounded-lg text-sm bg-red-100 text-red-700 hidden" role="alert">
            <!-- Error messages go here -->
        </div>

    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const dobInput = document.getElementById('dobInput');
            const calculateBtn = document.getElementById('calculateBtn');
            const resultOutput = document.getElementById('resultOutput');
            const ageDisplay = document.getElementById('ageDisplay');
            const nextBirthday = document.getElementById('nextBirthday');
            const totalAgeDisplay = document.getElementById('totalAgeDisplay');
            const messageBox = document.getElementById('messageBox');

            // Set the max attribute of the date input to today's date
            const today = new Date();
            const todayISO = today.toISOString().split('T')[0];
            dobInput.max = todayISO;

            // Helper function to show messages (instead of alert())
            function showMessage(message, type = 'error') {
                messageBox.textContent = message;
                messageBox.className = 'mt-4 p-3 rounded-lg text-sm ' + 
                    (type === 'error' ? 'bg-red-100 text-red-700' : 'bg-green-100 text-green-700');
                messageBox.classList.remove('hidden');
                resultOutput.classList.add('hidden');
            }

            function hideMessage() {
                messageBox.classList.add('hidden');
            }

            function getDaysInMonth(year, month) {
                // Date(year, month + 1, 0) gives the last day of the current month (month is 0-indexed)
                return new Date(year, month + 1, 0).getDate();
            }

            function calculateAge() {
                hideMessage();
                const dobValue = dobInput.value;

                if (!dobValue) {
                    showMessage("Please select your date of birth.");
                    return;
                }

                // Parse the DOB string into a Date object
                const birthDate = new Date(dobValue);

                const now = new Date();
                if (birthDate >= now) {
                    showMessage("Date of birth cannot be today or in the future.");
                    return;
                }

                // --- 1. Calculate Exact Calendar Age (Years, Months, Days) ---
                let y = now.getFullYear() - birthDate.getFullYear();
                let m = now.getMonth() - birthDate.getMonth();
                let d = now.getDate() - birthDate.getDate();

                // Adjust for days
                if (d < 0) {
                    m--;
                    const prevMonthDays = getDaysInMonth(now.getFullYear(), now.getMonth() - 1);
                    d += prevMonthDays;
                }

                // Adjust for months
                if (m < 0) {
                    y--;
                    m += 12;
                }

                // Calculate days until next birthday (Optional extra feature)
                let nextBday = new Date(now.getFullYear(), birthDate.getMonth(), birthDate.getDate());
                if (nextBday < now) {
                    // If the birthday this year has passed, set it for next year
                    nextBday.setFullYear(now.getFullYear() + 1);
                }

                const timeDiffMillis = nextBday.getTime() - now.getTime();
                const daysUntilBday = Math.ceil(timeDiffMillis / (1000 * 60 * 60 * 24));
                
                let nextBdayText = '';
                if (daysUntilBday === 0) {
                    nextBdayText = "🥳 Happy Birthday Today!";
                } else if (daysUntilBday === 1) {
                    nextBdayText = "Your next birthday is tomorrow!";
                } else {
                    nextBdayText = `Your next birthday is in ${daysUntilBday} days.`;
                }

                // Display Exact Age results
                ageDisplay.innerHTML = `
                    <span class="text-indigo-600">${y}</span> Years, 
                    <span class="text-indigo-600">${m}</span> Months, and 
                    <span class="text-indigo-600">${d}</span> Days
                `;
                nextBirthday.textContent = nextBdayText;


                // --- 2. Calculate Total Time Lived (Weeks, Total Months, Minutes, Seconds) ---
                const totalTimeMillis = now.getTime() - birthDate.getTime();

                // Calculate total days, hours, etc. from milliseconds
                const totalSeconds = Math.floor(totalTimeMillis / 1000);
                const totalMinutes = Math.floor(totalSeconds / 60);
                const totalHours = Math.floor(totalMinutes / 60);
                const totalDays = Math.floor(totalHours / 24);
                
                // Calculations requested by user
                const totalWeeks = Math.floor(totalDays / 7);
                // Total months using average days per month (365.25 / 12 = 30.4375)
                const totalMonthsRough = Math.floor(totalDays / 30.4375); 

                // Array of metrics to display
                const metrics = [
                    { value: totalMonthsRough, unit: 'Total Months' },
                    { value: totalWeeks, unit: 'Total Weeks' },
                    { value: totalMinutes, unit: 'Total Minutes' },
                    { value: totalSeconds, unit: 'Total Seconds' }
                ];

                // Generate HTML for total metrics
                totalAgeDisplay.innerHTML = metrics.map(metric => `
                    <div class="total-metric bg-indigo-50 border border-indigo-200 shadow-sm">
                        <p class="text-xl font-extrabold text-indigo-700">${metric.value.toLocaleString()}</p>
                        <p class="text-xs text-gray-500 uppercase font-semibold mt-1">${metric.unit}</p>
                    </div>
                `).join('');

                // Show the overall result box
                resultOutput.classList.remove('hidden');
            }

            calculateBtn.addEventListener('click', calculateAge);
            dobInput.addEventListener('keydown', (e) => {
                if (e.key === 'Enter') {
                    calculateAge();
                }
            });
        });
    </script>
</body>
</html>


