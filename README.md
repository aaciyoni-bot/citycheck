<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MuniPay - מערכת ריכוז דוחות ארצית</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Assistant:wght@300;400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { font-family: 'Assistant', sans-serif; background-color: #f8fafc; }
        .glass-effect { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(10px); }
        .step-transition { transition: all 0.4s ease-in-out; }
        .hidden-step { display: none; opacity: 0; transform: translateY(20px); }
        .active-step { display: block; opacity: 1; transform: translateY(0); }
        
        .scan-line {
            height: 2px;
            background: linear-gradient(90deg, transparent, #2563eb, transparent);
            position: absolute;
            width: 100%;
            top: 0;
            animation: scanMove 1.5s linear infinite;
        }
        @keyframes scanMove {
            0% { top: 0; }
            100% { top: 100%; }
        }
        .active-plan { border-color: #2563eb !important; background-color: #eff6ff !important; }
    </style>
</head>
<body class="min-h-screen text-slate-800">

    <nav class="bg-white border-b border-slate-200 px-6 py-4 sticky top-0 z-50">
        <div class="max-w-6xl mx-auto flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div class="bg-blue-600 p-2 rounded-lg text-white">
                    <i class="fas fa-file-invoice-dollar text-xl"></i>
                </div>
                <h1 class="text-2xl font-bold text-slate-900 tracking-tight">Muni<span class="text-blue-600">Pay</span></h1>
            </div>
            <div class="hidden md:flex items-center gap-6 text-sm font-medium text-slate-600">
                <span class="text-slate-400">בשיתוף פורום ה-15 ומשכ"ל</span>
            </div>
        </div>
    </nav>

    <main class="max-w-4xl mx-auto p-6 mt-8">

        <section id="step-search" class="active-step step-transition glass-effect p-10 rounded-3xl shadow-xl border border-white text-center">
            <div class="mb-10">
                <h2 class="text-3xl font-bold mb-3">איתור דוחות וחובות מוניציפליים</h2>
                <p class="text-slate-500">סריקה מקיפה ב-257 רשויות מקומיות בו-זמנית</p>
            </div>

            <div class="max-w-md mx-auto mb-8">
                <label class="block text-sm font-semibold mb-3 mr-1 text-right">מספר תעודת זהות</label>
                <input type="text" id="input-tz" placeholder="9 ספרות" class="w-full px-6 py-4 rounded-2xl border border-slate-200 focus:ring-4 focus:ring-blue-100 focus:border-blue-600 outline-none transition text-2xl font-mono tracking-widest text-center">
            </div>

            <button onclick="startNationalScanning()" class="w-full md:w-auto bg-blue-600 hover:bg-blue-700 text-white font-bold py-5 px-16 rounded-2xl shadow-lg shadow-blue-200 transform transition hover:-translate-y-1 active:scale-95 text-xl">
                בצע סריקה ארצית <i class="fas fa-microchip mr-2"></i>
            </button>
        </section>

        <section id="step-scanning" class="hidden-step step-transition glass-effect p-12 rounded-3xl shadow-xl text-center relative overflow-hidden">
            <div class="scan-line"></div>
            <div class="inline-block relative w-24 h-24 mb-6">
                <div class="absolute inset-0 border-4 border-blue-100 rounded-full"></div>
                <div class="absolute inset-0 border-4 border-t-blue-600 rounded-full animate-spin"></div>
                <div class="absolute inset-0 flex items-center justify-center">
                    <i class="fas fa-globe-israel text-3xl text-blue-600"></i>
                </div>
            </div>
            
            <h3 class="text-2xl font-bold mb-2">מבצע שאילתה מול בסיס הנתונים הארצי</h3>
            <div class="flex items-center justify-center gap-2 text-blue-600 font-bold mb-6 text-xl">
                נסרקו: <span id="scan-count">0</span> / 257 רשויות
            </div>
            
            <div id="city-flicker" class="h-12 text-slate-400 text-lg font-medium italic">
                מתחבר למערכת הגבייה...
            </div>

            <div class="max-w-md mx-auto mt-8">
                <div class="w-full bg-slate-100 h-3 rounded-full overflow-hidden">
                    <div id="total-progress" class="bg-blue-600 h-full w-0 transition-all duration-100"></div>
                </div>
            </div>
        </section>

        <section id="step-results" class="hidden-step step-transition">
            <div class="flex flex-col lg:flex-row gap-6">
                <div class="flex-grow bg-white p-6 rounded-3xl shadow-lg border border-slate-100">
                    <div class="flex justify-between items-center mb-6">
                        <h3 class="text-xl font-bold">נמצאו 6 דוחות פעילים ברשויות</h3>
                        <span class="bg-red-100 text-red-700 px-4 py-1.5 rounded-full text-xs font-bold border border-red-200">סטטוס: חוב בטיפול</span>
                    </div>

                    <div class="overflow-x-auto">
                        <table class="w-full text-right">
                            <thead>
                                <tr class="text-slate-400 text-sm border-b">
                                    <th class="pb-3">עירייה</th>
                                    <th class="pb-3">מהות הדוח</th>
                                    <th class="pb-3">תאריך</th>
                                    <th class="pb-3 text-left">סכום</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-slate-100">
                                <tr><td class="py-4 font-bold text-blue-900">פתח תקווה</td><td class="py-4">חניה על מדרכה</td><td class="py-4">15/04/24</td><td class="py-4 font-bold text-left">250 ₪</td></tr>
                                <tr><td class="py-4 font-bold text-blue-900">נס ציונה</td><td class="py-4">חניה בכחול לבן</td><td class="py-4">02/03/24</td><td class="py-4 font-bold text-left">100 ₪</td></tr>
                                <tr><td class="py-4 font-bold text-blue-900">שדרות</td><td class="py-4">השלכת פסולת חריגה</td><td class="py-4">20/02/24</td><td class="py-4 font-bold text-left">320 ₪</td></tr>
                                <tr><td class="py-4 font-bold text-blue-900">ירושלים</td><td class="py-4">נסיעה בנת"צ</td><td class="py-4">12/01/24</td><td class="py-4 font-bold text-left">500 ₪</td></tr>
                                <tr><td class="py-4 font-bold text-blue-900">רעננה</td><td class="py-4">הפרעה לתנועה</td><td class="py-4">10/05/24</td><td class="py-4 font-bold text-left">250 ₪</td></tr>
                                <tr><td class="py-4 font-bold text-blue-900">באר שבע</td><td class="py-4">חניית נכה (ללא תג)</td><td class="py-4">05/04/24</td><td class="py-4 font-bold text-left">1,000 ₪</td></tr>
                            </tbody>
                        </table>
                    </div>
                </div>

                <div class="w-full lg:w-80 bg-slate-900 text-white p-8 rounded-3xl shadow-xl">
                    <h4 class="text-slate-400 font-semibold mb-2">סה"כ חוב מאוחד</h4>
                    <div class="text-4xl font-black mb-8">2,420 ₪</div>
                    <div class="space-y-4">
                        <button onclick="showArrangement()" class="w-full bg-blue-600 text-white font-bold py-4 rounded-2xl hover:bg-blue-500 transition shadow-lg">שלם הכל עכשיו</button>
                        <button onclick="showArrangement()" class="w-full bg-white bg-opacity-10 border border-slate-700 text-white font-bold py-4 rounded-2xl hover:bg-opacity-20 transition">הסדר תשלומים מרוכז</button>
                    </div>
                </div>
            </div>
        </section>

        <section id="step-arrangement" class="hidden-step step-transition glass-effect p-8 rounded-3xl shadow-xl border-2 border-blue-100">
            <button onclick="backToResults()" class="text-blue-600 font-semibold mb-6 hover:underline"><i class="fas fa-arrow-right ml-1"></i> חזרה</button>
            <h3 class="text-2xl font-bold mb-2">הסדר תשלומים מרוכז (UniPlan)</h3>
            <p class="text-slate-500 mb-8">ניתן לפרוס את כלל הדוחות לתשלומים חודשיים שווים</p>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-8">
                <div onclick="selectInstallments(3)" id="plan-3" class="border-2 border-slate-100 p-6 rounded-2xl cursor-pointer text-center active-plan">
                    <div class="font-bold">3 תשלומים</div>
                    <div class="text-xl font-bold mt-1">806 ₪</div>
                </div>
                <div onclick="selectInstallments(6)" id="plan-6" class="border-2 border-slate-100 p-6 rounded-2xl cursor-pointer text-center">
                    <div class="font-bold">6 תשלומים</div>
                    <div class="text-xl font-bold mt-1">403 ₪</div>
                </div>
                <div onclick="selectInstallments(12)" id="plan-12" class="border-2 border-slate-100 p-6 rounded-2xl cursor-pointer text-center">
                    <div class="font-bold">12 תשלומים</div>
                    <div class="text-xl font-bold mt-1">201 ₪</div>
                </div>
            </div>

            <button onclick="completeProcess()" class="w-full bg-green-600 hover:bg-green-700 text-white font-bold py-5 rounded-2xl text-xl">אישור והפעלת הסדר</button>
        </section>

        <div id="step-success" class="hidden-step step-transition glass-effect p-12 rounded-3xl shadow-xl text-center">
            <div class="w-20 h-20 bg-green-100 text-green-600 rounded-full flex items-center justify-center mx-auto mb-6 text-3xl"><i class="fas fa-check"></i></div>
            <h3 class="text-3xl font-bold mb-4">הסדר התשלומים הופעל!</h3>
            <p class="text-slate-500 mb-8">הודעות על הקפאת הליכים נשלחו לכלל הרשויות הרלוונטיות.</p>
            <button onclick="location.reload()" class="bg-blue-600 text-white font-bold py-3 px-10 rounded-xl">חזרה להתחלה</button>
        </div>

    </main>

    <script>
        const cities = ["ירושלים", "תל אביב", "חיפה", "פתח תקווה", "ראשון לציון", "אשדוד", "באר שבע", "נתניה", "חולון", "בני ברק", "רמת גן", "רחובות", "בת ים", "הרצליה", "כפר סבא", "חדרה", "רעננה", "מודיעין", "בית שמש", "לוד", "רמלה", "נהריה", "עכו", "כרמיאל", "אילת", "טבריה", "ערד", "שדרות", "נס ציונה"];

        function switchStep(fromId, toId) {
            document.getElementById(fromId).classList.remove('active-step');
            document.getElementById(fromId).classList.add('hidden-step');
            
            const nextStep = document.getElementById(toId);
            nextStep.classList.remove('hidden-step');
            nextStep.classList.add('active-step');
        }

        async function startNationalScanning() {
            const tz = document.getElementById('input-tz').value;
            if (tz.length < 8) {
                alertCustom('נא להזין מספר תעודת זהות תקין');
                return;
            }

            switchStep('step-search', 'step-scanning');
            
            const countEl = document.getElementById('scan-count');
            const progressEl = document.getElementById('total-progress');
            const flickerEl = document.getElementById('city-flicker');
            
            let current = 0;
            const total = 257;
            
            const interval = setInterval(() => {
                current += Math.floor(Math.random() * 15) + 5;
                if (current >= total) {
                    current = total;
                    clearInterval(interval);
                    setTimeout(() => switchStep('step-scanning', 'step-results'), 600);
                }
                
                countEl.innerText = current;
                progressEl.style.width = (current / total * 100) + '%';
                
                const randomCity = cities[Math.floor(Math.random() * cities.length)];
                flickerEl.innerText = `בודק מול עיריית ${randomCity}...`;
            }, 40);
        }

        function showArrangement() {
            switchStep('step-results', 'step-arrangement');
        }

        function backToResults() {
            switchStep('step-arrangement', 'step-results');
        }

        function selectInstallments(count) {
            document.querySelectorAll('#step-arrangement .border-2').forEach(el => el.classList.remove('active-plan'));
            document.getElementById(`plan-${count}`).classList.add('active-plan');
        }

        function completeProcess() {
            switchStep('step-arrangement', 'step-success');
        }

        function alertCustom(msg) {
            const div = document.createElement('div');
            div.className = "fixed bottom-10 left-1/2 transform -translate-x-1/2 bg-slate-900 text-white px-8 py-4 rounded-2xl shadow-2xl z-[100]";
            div.innerHTML = `<i class="fas fa-exclamation-circle text-red-400 ml-2"></i> ${msg}`;
            document.body.appendChild(div);
            setTimeout(() => div.remove(), 3000);
        }
    </script>
</body>
</html>
