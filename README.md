<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nasser A Elmrazy</title>
    <style>
        /* أنماط CSS (مماثلة للنسخ السابقة) */
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(45deg, #ff00ff, #0000ff);
            color: white;
            text-align: center;
            margin: 0;
            padding: 0;
        }
        .quiz-container {
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border-radius: 10px;
            width: 80%;
            margin: auto;
            margin-top: 20px;
            box-shadow: 0px 0px 20px white;
        }
        .question-buttons {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(50px, 1fr));
            gap: 5px;
            margin-bottom: 20px;
        }
        .question-buttons button {
            padding: 10px;
            font-size: 14px;
            background: white;
            color: black;
            border-radius: 5px;
            cursor: pointer;
            border: none;
            transition: background-color 0.3s ease;
        }
        .question-buttons button:hover {
            background: lime;
        }
        .question-buttons button.selected {
            background: lime;
            color: black;
        }
        .question-buttons button.correct {
            background-color: #4CAF50;
            color: white;
        }
        .question-buttons button.incorrect {
            background-color: #f44336;
            color: white;
        }
        .question-buttons button.prize-obtained { /* الكلاس الجديد لتمييز زر الجائزة */
            background-color: gold;
            color: black;
            font-weight: bold;
            animation: pulse 1s infinite alternate; /* إضافة تأثير نبض اختياري */
        }
        @keyframes pulse {
            from { transform: scale(1); }
            to { transform: scale(1.1); }
        }
        #options button {
            display: block;
            width: 80%;
            margin: 5px auto;
            padding: 10px;
            font-size: 18px;
            background: white;
            color: black;
            border-radius: 5px;
            cursor: pointer;
            border: none;
            transition: background-color 0.3s ease;
        }
        #options button:hover {
            background: lime;
        }
        #timer {
            font-size: 20px;
            font-weight: bold;
            margin-top: 10px;
        }
        #result {
            font-size: 20px;
            font-weight: bold;
            margin-top: 20px;
        }
        #team-setup {
            margin-bottom: 20px;
            text-align: center;
        }
        #team-setup input {
            padding: 10px;
            font-size: 16px;
            margin: 5px;
            border: 2px solid white;
            border-radius: 5px;
            background-color: rgba(255, 255, 255, 0.7);
            color: black;
        }
        #team-setup button {
            padding: 10px 15px;
            font-size: 16px;
            margin: 5px;
            background: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }
        #team-setup button:hover {
            background: #45a049;
        }
        #team-selection {
            margin-bottom: 20px;
            text-align: center;
        }
        #team-selection button {
            padding: 10px 15px;
            font-size: 16px;
            margin: 0 5px;
            background: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }
        #team-selection button:hover {
            background: #45a049;
        }
        .team-score {
            font-size: 18px;
            font-weight: bold;
            margin: 10px 0;
            color: yellow;
        }
        @media (max-width: 768px) {
            .quiz-container {
                width: 95%;
            }
            .question-buttons button {
                font-size: 12px;
                padding: 8px;
            }
            #options button {
                font-size: 16px;
            }
            #team-setup input {
                font-size: 14px;
                padding: 8px;
            }
            #team-setup button {
                font-size: 14px;
                padding: 8px 12px;
            }
            #team-selection button {
                font-size: 14px;
                padding: 8px 12px;
            }
            .team-score {
                font-size: 16px;
            }
        }
        .excluded-team {
            color: red !important;
            text-decoration: line-through;
            font-style: italic;
        }
    </style>
</head>
<body>
    <div class="quiz-container">
        <h1>❤️مسابقة لعيوني </h1>
        <div id="team-setup">
            <p>أدخل أسماء الفرق وعددها:</p>
            <input type="number" id="num-teams" placeholder="عدد الفرق" min="1">
            <div id="team-names-inputs"></div>
            <button onclick="setupTeams()">إعداد الفرق</button>
        </div>
        <div id="team-selection" style="display: none;">
            <p>اختر الفريق:</p>
        </div>
        <div class="question-buttons" id="questionButtons" style="display: none;"></div>
        <h2 id="questionText" style="display: none;"></h2>
        <div id="options" style="display: none;"></div>
        <p id="timer" style="display: none;"></p>
        <button id="startTimer" onclick="startCountdown()" style="display: none;">⏳ بدء المؤقت</button>
        <p id="result" style="display: none;"></p>
        <button id="nextButton" onclick="nextQuestion()" style="display: none; margin-top: 20px;">السؤال التالي ➡️</button>

        <div id="admin-controls" style="margin-top: 20px; border-top: 1px solid white; padding-top: 20px;">
            <h3>إدارة المسابقة</h3>
            <div>
                <h4>تعديل النقاط</h4>
                <select id="select-team-score"></select>
                <button onclick="incrementScore()">زيادة نقطة</button>
                <button onclick="decrementScore()">إنقاص نقطة</button>
                <input type="number" id="set-score-value" placeholder="قيمة النقطة">
                <button onclick="setScore()">تحديد قيمة النقطة</button>
            </div>
            <div style="margin-top: 10px;">
                <h4>إضافة فريق جديد</h4>
                <input type="text" id="new-team-name" placeholder="اسم الفريق الجديد">
                <button onclick="addTeam()">إضافة فريق</button>
            </div>
            <div style="margin-top: 10px;">
                <h4>استبعاد فريق</h4>
                <select id="select-team-exclude"></select>
                <button onclick="excludeTeam()">استبعاد الفريق</button>
                <button onclick="includeTeam()">إعادة الفريق</button>
            </div>
        </div>
    </div>
    <script>
        let questions = [
            { question: "ما هو اسم الكوكب الذي يدور حوله أكبر عدد من الأقمار الطبيعية؟", options: ["المشتري", "زحل", "أورانوس", "نبتون"], answer: 1 },
            { question: "من هو أول شخص فاز بجائزة نوبل في الفيزياء مرتين؟", options: ["ألبرت أينشتاين", "ماري كوري", "ماكس بلانك", "نيلز بور"], answer: 1 },
            { question: "ما هي العملة الرسمية لدولة سويسرا؟", options: ["اليورو", "الفرنك السويسري", "الجنيه السويسري", "الدولار السويسري"], answer: 1 },
            { question: "ما هو اسم أطول نهر في قارة أوروبا؟", options: ["نهر الفولغا", "نهر الدانوب", "نهر الراين", "نهر الأورال"], answer: 0 },
            { question: "من كتب مسرحية 'هاملت'؟", options: ["تشارلز ديكنز", "ويليام شكسبير", "جين أوستن", "جورج برنارد شو"], answer: 1 },
            { question: "ما هو العنصر الأكثر وفرة في الغلاف الجوي للأرض؟", options: ["الأكسجين", "النيتروجين", "الأرجون", "ثاني أكسيد الكربون"], answer: 1 },
            { question: "ما هي عاصمة كندا؟", options: ["تورونتو", "مونتريال", "أوتاوا", "فانكوفر"], answer: 2 },
            { question: "ما هو اسم الحيوان الوطني لأستراليا؟", options: ["الكنغر", "الكوالا", "الإيمو", "الومبت"], answer: 0 },
            { question: "ما هي أكبر بحيرة في العالم من حيث المساحة؟", options: ["بحر قزوين", "بحيرة سوبيريور", "بحيرة فيكتوريا", "البحر الميت"], answer: 0 },
            { question: "ما هو اسم أول رئيس لجنوب أفريقيا بعد نهاية نظام الفصل العنصري؟", options: ["نيلسون مانديلا", "دي كليرك", "ثابو مبيكي", "جاكوب زوما"], answer: 0 },
            { question: "ما هي العملة الرسمية لدولة البرازيل؟", options: ["البيزو البرازيلي", "الريال البرازيلي", "الكروزيرو البرازيلي", "الدولار البرازيلي"], answer: 1 },
            { question: "ما هو اسم أطول سلسلة جبال في العالم؟", options: ["جبال الهيمالايا", "جبال الأنديز", "جبال روكي", "جبال الألب"], answer: 1 },
            { question: "من رسم لوحة 'ليلة النجوم'؟", options: ["مونيه", "فان جوخ", "دافنشي", "بيكاسو"], answer: 1 },
            { question: "ما هو اسم الكوكب الذي يعتبر أقرب كوكب إلى الشمس؟", options: ["الزهرة", "عطارد", "المريخ", "الأرض"], answer: 1 },
            { question: "ما هي عاصمة اليابان؟", options: ["كيوتو", "أوساكا", "طوكيو", "هيروشيما"], answer: 2 },
            { question: "ما هو اسم الطائر الوطني للولايات المتحدة الأمريكية؟", options: ["النسر الذهبي", "النسر الأصلع", "الصقر الشاهين", "البومة القرناء الكبيرة"], answer: 1 },
            { question: "ما هي أصغر دولة في العالم من حيث المساحة؟", options: ["موناكو", "ناورو", "الفاتيكان", "توفالو"], answer: 2 },
            { question: "ما هو اسم أول شخص مشى على سطح القمر؟", options: ["باز ألدرين", "يوري غاغارين", "نيل أرمسترونج", "جون جلين"], answer: 2 },
            { question: "ما هي العملة الرسمية لدولة الصين؟", options: ["الين الصيني", "اليوان الصيني", "الرنمينبي", "الدولار الصيني"], answer: 2 },
            { question: "ما هو اسم أطول نهر في العالم؟", options: ["نهر النيل", "نهر الأمازون", "نهر اليانغتسي", "نهر المسيسيبي"], answer: 1 },
            { question: "من اكتشف البنسلين؟", options: ["ماري كوري", "ألبرت أينشتاين", "ألكسندر فليمنج", "إسحاق نيوتن"], answer: 2 },
            { question: "ما هي عاصمة أستراليا؟", options: ["سيدني", "ملبورن", "كانبرا", "بريزبن"], answer: 2 },
            { question: "ما هو اسم أكبر جزيرة في العالم؟", options: ["أستراليا", "غرينلاند", "بورنيو", "مدغشقر"], answer: 1 },
            { question: "ما هي العملة الرسمية لدولة الهند؟", options: ["الروبية الهندية", "الروبل الهندي", "البيزو الهندي", "الدولار الهندي"], answer: 0 },
            { question: "ما هو اسم أعلى جبل في العالم؟", options: ["كي2", "كانغشينجونغا", "إيفرست", "لوتس"], answer: 2 },
            { question: "من كتب رواية 'كبرياء وتحامل'؟", options: ["تشارلز ديكنز", "جين أوستن", "الأخوات برونتي", "جورج إليوت"], answer: 1 },
            { question: "ما هي عاصمة فرنسا؟", options: ["برلين", "مدريد", "باريس", "روما"], answer: 2 },
            { question: "ما هو اسم أسرع حيوان بري؟", options: ["الأسد", "الفهد", "الحمار الوحشي", "الغزال"], answer: 1 },
            { question: "ما هي العملة الرسمية لدولة كندا؟", options: ["الدولار الكندي", "اليورو", "الجنيه الكندي", "الفرنك الكندي"], answer: 0 },
            { question: "ما هو اسم أكبر صحراء في العالم؟", options: ["الصحراء الكبرى", "الصحراء القطبية الجنوبية", "الصحراء القطبية الشمالية", "صحراء الربع الخالي"], answer: 1 },
            { question: "من اخترع المصباح الكهربائي؟", options: ["نيكولا تيسلا", "توماس إديسون", "بنجامين فرانكلين", "ألكسندر جراهام بيل"], answer: 1 },
            { question: "ما هي عاصمة ألمانيا؟", options: ["هامبورغ", "ميونخ", "برلين", "فرانكفورت"], answer: 2 },
            { question: "ما هو اسم أعمق نقطة في محيطات العالم؟", options: ["خندق ماريانا", "خندق بورتوريكو", "خندق تونغا", "خندق اليابان"], answer: 0 },
            { question: "ما هي العملة الرسمية لدولة اليابان؟", options: ["الين الياباني", "الوان الياباني", "الدولار الياباني", "الروبية اليابانية"], answer: 0 },
            { question: "ما هو اسم أول رئيس للولايات المتحدة الأمريكية؟", options: ["جون آدمز", "توماس جيفرسون", "جورج واشنطن", "جيمس ماديسون"], answer: 2 },
            { question: "من كتب رواية 'دون كيشوت'؟", options: ["ميغيل دي سرفانتس", "غابرييل غارسيا ماركيز", "خورخي لويس بورخيس", "إيزابيل الليندي"], answer: 0 },
            { question: "ما هي عاصمة إسبانيا؟", options: ["برشلونة", "فالنسيا", "مدريد", "إشبيلية"], answer: 2 },
            { question: "ما هو اسم أكبر كوكب في نظامنا الشمسي؟", options: ["زحل", "المشتري", "أورانوس", "نبتون"], answer: 1 },
            { question: "ما هي العملة الرسمية لدولة المملكة المتحدة؟", options: ["اليورو", "الدولار البريطاني", "الجنيه الإسترليني", "الفرنك البريطاني"], answer: 2 },
            { question: "ما هو اسم أطول نهر في قارة أمريكا الجنوبية؟", options: ["نهر الأمازون", "نهر الأورينوكو", "نهر بارانا", "نهر ساو فرانسيسكو"], answer: 0 },
            { question: "من اكتشف الجاذبية الأرضية؟", options: ["ألبرت أينشتاين", "إسحاق نيوتن", "غاليليو غاليلي", "نيكولاوس كوبرنيكوس"], answer: 1 },
            { question: "ما هي عاصمة روسيا؟", options: ["سانت بطرسبرغ", "كييف", "موسكو", "مينسك"], answer: 2 },
            { question: "ما هو اسم أكبر قارة في العالم؟", options: ["أفريقيا", "آسيا", "أوروبا", "أمريكا"], answer: 1 },
            { question: "ما هي العملة الرسمية لدولة جنوب أفريقيا؟", options: ["الراند الجنوب أفريقي", "الشيلينغ الجنوب أفريقي", "البيزو الجنوب أفريقي", "الدولار الجنوب أفريقي"], answer: 0 },
            { question: "ما هو اسم أطول جسر في العالم؟", options: ["جسر البوابة الذهبية", "جسر أكاشي كايكيو", "جسر دونغهاي", "جسر فيري بريدج"], answer: 1 },
            { question: "من كتب رواية 'موبي ديك'؟", options: ["هيرمان ملفيل", "ناثانيال هوثورن", "إدغار آلان بو", "رالف والدو إيمرسون"], answer: 0 },
            { question: "ما هي عاصمة الصين؟", options: ["شنغهاي", "هونغ كونغ", "بكين", "غوانزو"], answer: 2 },
            { question: "ما هو اسم أصغر كوكب في نظامنا الشمسي (بعد إعادة التصنيف)؟", options: ["المريخ", "عطارد", "بلوتو", "سيريس"], answer: 1 },
            { question: "ما هي العملة الرسمية لدولة أستراليا؟", options: ["الدولار الأسترالي", "اليورو", "الجنيه الأسترالي", "الفرنك الأسترالي"], answer: 0 },
            { question: "ما هو اسم أطول حيوان بري؟", options: ["الفيل الأفريقي", "الزرافة", "وحيد القرن الأبيض", "فرس النهر"], answer: 1 },
            { question: "من اكتشف نظرية النسبية؟", options: ["إسحاق نيوتن", "ألبرت أينشتاين", "ستيفن هوكينج", "ماكس بلانك"], answer: 1 },
            { question: "ما هي عاصمة الهند؟", options: ["مومباي", "كلكتا", "نيودلهي", "تشيناي"], answer: 2 },
            { question: "ما هو اسم أكبر محيط في العالم؟", options: ["المحيط الأطلسي", "المحيط الهندي", "المحيط المتجمد الشمالي", "المحيط الهادئ"], answer: 3 },
            { question: "ما هي العملة الرسمية لدولة المكسيك؟", options: ["البيزو المكسيكي", "الدولار المكسيكي", "الروبية المكسيكية", "الكولون المكسيكي"], answer: 0 },
            { question: "ما هو اسم أطول نهر في قارة آسيا؟", options: ["نهر الغانج", "نهر اليانغتسي", "نهر الميكونغ", "نهر الأصفر"], answer: 1 },
            { question: "من كتب رواية 'مئة عام من العزلة'؟", options: ["إيزابيل الليندي", "خورخي لويس بورخيس", "غابرييل غارسيا ماركيز", "ماريو بارغاس يوسا"], answer: 2 },
            { question: "ما هي عاصمة البرازيل؟", options: ["ريو دي جانيرو", "ساو باولو", "برازيليا", "بيلو هوريزونتي"], answer: 2 },
            { question: "ما هو اسم أسرع طائر في العالم؟", options: ["النسر الذهبي", "الصقر الشاهين", "السنونو", "الحمامة"], answer: 1 },
            { question: "ما هي العملة الرسمية لدولة نيجيريا؟", options: ["النايرا النيجيري", "الشيلينغ النيجيري", "البيزو النيجيري", "الدولار النيجيري"], answer: 0 },
            { question: "ما هو اسم أطول كهف في العالم؟", options: ["كهف الماموث", "كهف سون دونغ", "كهف جيتا", "كهف كارلسباد"], answer: 0 },
            { question: "من اكتشف قانون الجاذبية العام؟", options: ["ألبرت أينشتاين", "إسحاق نيوتن", "غاليليو غاليلي", "يوهانس كيبلر"], answer: 1 },
            { question: "ما هي عاصمة الأرجنتين؟", options: ["ساو باولو", "ريو دي جانيرو", "بوينس آيرس", "سانتياغو"], answer: 2 },
            { question: "ما هو اسم أكبر حيوان على وجه الأرض؟", options: ["الفيل الأفريقي", "الحوت الأزرق", "الزرافة", "فرس النهر"], answer: 1 },
            { question: "ما هي العملة الرسمية لدولة تركيا؟", options: ["الليرة التركية", "الروبية التركية", "الدينار التركي", "اليورو"], answer: 0 },
            { question: "ما هو اسم أطول نهر في قارة أفريقيا؟", options: ["نهر الكونغو", "نهر النيل", "نهر النيجر", "نهر الزمبيزي"], answer: 1 },
            { question: "من كتب رواية 'الحرب والسلام'؟", options: ["ليو تولستوي", "فيودور دوستويفسكي", "أنطون تشيخوف", "نيكولاي غوغول"], answer: 0 },
            { question: "ما هي عاصمة مصر؟", options: ["الإسكندرية", "الأقصر", "القاهرة", "الجيزة"], answer: 2 },
            { question: "ما هو اسم أسرع مخلوق بحري؟", options: ["الدلفين", "القرش الأبيض الكبير", "سمكة أبو سيف", "الحوت القاتل"], answer: 2 },
            { question: "ما هي العملة الرسمية لدولة جنوب أفريقيا؟", options: ["الراند", "الشيلينغ", "البيزو", "الدولار"], answer: 0 },
            { question: "ما هو اسم أكبر بركان نشط في العالم؟", options: ["ماونا لوا", "جبل إتنا", "جبل فيزوف", "جبل فوجي"], answer: 0 },
            { question: "من اكتشف عنصر الأكسجين؟", options: ["ماري كوري", "أنطوان لافوازييه", "ديمتري مندليف", "جوزيف بريستلي"], answer: 3 },
            { question: "ما هي عاصمة السعودية؟", options: ["جدة", "الرياض", "مكة", "المدينة"], answer: 1 },
            { question: "ما هو اسم أكبر خليج في العالم؟", options: ["خليج المكسيك", "خليج البنغال", "خليج هدسون", "الخليج العربي"], answer: 0 },
            { question: "ما هي العملة الرسمية لدولة المغرب؟", options: ["الدينار", "الدرهم", "الفرنك", "الريال"], answer: 1 },
            { question: "ما هو اسم أطول شجرة في العالم؟", options: ["شجرة الجنرال شيرمان", "شجرة هايبريون", "شجرة الرئيس", "شجرة ميثوسيلاه"], answer: 1 },
            { question: "من كتب رواية 'الإخوة كارامازوف'؟", options: ["ليو تولستوي", "فيودور دوستويفسكي", "أنطون تشيخوف", "إيفان تورغينيف"], answer: 1 },
            { question: "ما هي عاصمة العراق؟", options: ["البصرة", "الموصل", "بغداد", "أربيل"], answer: 2 },
            { question: "ما هو اسم أسرع سيارة في العالم (حسب السرعة القصوى المعلنة)؟", options: ["بوغاتي شيرون سوبر سبورت 300+", "كونيجسيج جيسكو أبسولوت", "هينيسي فينوم F5", "إس إس سي تواتارا"], answer: 0 },
            { question: "ما هي العملة الرسمية لدولة الإمارات العربية المتحدة؟", options: ["الريال", "الدينار", "الدرهم", "الشيلينغ"], answer: 2 },
            { question: "ما هو اسم أطول نهر في قارة أستراليا؟", options: ["نهر موري", "نهر دارلينج", "نهر فيتزروي", "نهر بريزبن"], answer: 0 },
            { question: "من كتب مسرحية 'روميو وجولييت'؟", options: ["كريستوفر مارلو", "بن جونسون", "ويليام شكسبير", "توماس كيد"], answer: 2 },
            { question: "ما هي عاصمة سوريا؟", options: ["حلب", "اللاذقية", "دمشق", "حمص"], answer: 2 },
            { question: "ما هو اسم أعمق بحيرة في العالم؟", options: ["بحر قزوين", "بحيرة سوبيريور", "بحيرة فيكتوريا", "بحيرة بايكال"], answer: 3 },
            { question: "ما هي العملة الرسمية لدولة قطر؟", options: ["الريال القطري", "الدينار القطري", "الدرهم القطري", "الشيلينغ القطري"], answer: 0 },
            { question: "ما هو اسم أطول حيوان بحري؟", options: ["الحوت الأزرق", "الحبار العملاق", "قنديل البحر الأسد", "الدودة الشريطية البحرية"], answer: 0 },
            { question: "من اكتشف قانون حفظ الطاقة؟", options: ["ألبرت أينشتاين", "إسحاق نيوتن", "جيمس جول", "هيرمان فون هيلمهولتز"], answer: 3 },
            { question: "ما هي عاصمة الأردن؟", options: ["الزرقاء", "إربد", "عمان", "العقبة"], answer: 2 },
            { question: "ما هو اسم أكبر جزيرة غير قارية في العالم؟", options: ["غرينلاند", "نيو غينيا", "بورنيو", "مدغشقر"], answer: 0 },
            { question: "ما هي العملة الرسمية لدولة لبنان؟", options: ["الليرة اللبنانية", "الجنيه اللبناني", "الدولار اللبناني", "الفرنك اللبناني"], answer: 0 },
            { question: "ما هو اسم أطول نفق في العالم؟", options: ["نفق سيكان", "نفق القناة", "نفق غوتهارد الأساسي", "نفق ليردال"], answer: 2 },
            { question: "من كتب رواية 'الجريمة والعقاب'؟", options: ["ليو تولستوي", "فيودور دوستويفسكي", "أنطون تشيخوف", "إيفان تورغينيف"], answer: 1 },
            { question: "ما هي عاصمة الكويت؟", options: ["مدينة الكويت", "السالمية", "الفروانية", "الأحمدي"], answer: 0 },
            { question: "ما هو اسم أسرع حيوان في المحيط؟", options: ["الدلفين", "القرش الأبيض الكبير", "سمكة أبو شراع", "التونة ذات الزعانف الزرقاء"], answer: 2 },
            { question: "ما هي العملة الرسمية لدولة عمان؟", options: ["الريال العماني", "الدينار العماني", "الدرهم العماني", "الشيلينغ العماني"], answer: 0 },
            { question: "ما هو اسم أطول نهر في قارة أفريقيا؟", options: ["نهر الكونغو", "نهر النيل", "نهر النيجر", "نهر السنغال"], answer: 1 },
            { question: "من كتب مسرحية 'عطيل'؟", options: ["كريستوفر مارلو", "بن جونسون", "ويليام شكسبير", "توماس كيد"], answer: 2 },
            { question: "ما هي عاصمة اليمن؟", options: ["عدن", "صنعاء", "تعز", "الحديدة"], answer: 1 },
            { question: "ما هو اسم أعمق وادٍ في العالم؟", options: ["الوادي المتصدع الكبير", "وادي كولكا", "وادي يارلونغ تسانغبو", "جراند كانيون"], answer: 2 },
            { question: "ما هي العملة الرسمية لدولة البحرين؟", options: ["الدينار البحريني", "الريال البحريني", "الدرهم البحريني", "الشيلينغ البحريني"], answer: 0 },
            { question: "ما هو اسم أطول كهف جليدي في العالم؟", options: ["كهف مينشينهال", "كهف سكافتافيل", "كهف الكريستال في آيسلندا", "كهف ريد فلوت"], answer: 0 },
            { question: "من اكتشف قانون بقاء الكتلة؟", options: ["جون دالتون", "جوزيف بروست", "أنطوان لافوازييه", "روبرت بويل"], answer: 2 },
            { question: "ما هي عاصمة لبنان؟", options: ["بيروت", "صيدا", "طرابلس", "صور"], answer: 0 },
            { question: "ما هو اسم أكبر حقل نفطي بري في العالم؟", options: ["حقل الغوار", "حقل برقان", "حقل السفانية", "حقل كاشاغان"], answer: 0 },
            { question: "ما هي العملة الرسمية لدولة السودان؟", options: ["الجنيه السوداني", "الدينار السوداني", "الريال السوداني", "الشيلينغ السوداني"], answer: 0 },
            { question: "ما هو اسم أطول نهر في قارة أمريكا الشمالية؟", options: ["نهر المسيسيبي", "نهر اليوكون", "نهر ماكينزي", "نهر كولورادو"], answer: 0 },
            { question: "من كتب رواية 'البؤساء'؟", options: ["فيكتور هوغو", "ألكسندر دوما", "جوستاف فلوبير", "إميل زولا"], answer: 0 },
            { question: "ما هي عاصمة ليبيا؟", options: ["بنغازي", "طرابلس", "مصراتة", "سبها"], answer: 1 },
            { question: "ما هو اسم أسرع حشرة طائرة؟", options: ["اليعسوب", "الفراشة", "النحلة", "الذبابة"], answer: 0 },
            { question: "ما هي العملة الرسمية لدولة فلسطين؟", options: ["الدينار الفلسطيني", "الشيكل الإسرائيلي", "الجنيه الفلسطيني", "الدولار الأمريكي"], answer: 1 },
            { question: "ما هو اسم أطول شلال في قارة آسيا؟", options: ["شلالات جوا", "شلالات هوانغشان", "شلالات كيانتو", "شلالات ناتشي"], answer: 2 },
            { question: "من اكتشف الأشعة السينية؟", options: ["ماري كوري", "فيلهلم رونتجن", "ألبرت أينشتاين", "نيلز بور"], answer: 1 },
            { question: "ما هي عاصمة فلسطين (المعلنة)؟", options: ["القدس", "رام الله", "غزة", "بيت لحم"], answer: 0 },
            { question: "ما هو اسم أكبر منجم ذهب في العالم؟", options: ["منجم غراسبرغ", "منجم أوليمبيادا", "منجم موروتاو", "منجم سوبر بيت"], answer: 2 },
            { question: "ما هي العملة الرسمية لدولة سوريا؟", options: ["الليرة السورية", "الجنيه السوري", "الدينار السوري", "الريال السوري"], answer: 0 },
            { question: "ما هو اسم أطول جسر معلق في العالم؟", options: ["جسر أكاشي كايكيو", "جسر البوابة الذهبية", "جسر همبر", "جسر فيرازانو-ناروز"], answer: 0 }
        ];

        let score = {};
        let answered = false;
        let currentQuestionIndex = 0;
        let countdown;
        let selectedQuestions = [];
        let teams = [];
        let currentTeam = null;
        let prizeLocations = {};
        const prizes = ["جوهرة", "موهيتو", "نعنش", "بيالة شاي", "دونات", "شطة"];
        let excludedTeams = [];

        function initializeGame() {
            document.getElementById("team-setup").style.display = "block";
            document.getElementById("team-selection").style.display = "none";
            document.getElementById("questionButtons").style.display = "none";
            document.getElementById("questionText").style.display = "none";
            document.getElementById("options").style.display = "none";
            document.getElementById("timer").style.display = "none";
            document.getElementById("result").style.display = "none";
            document.getElementById("nextButton").style.display = "none";

            const availableIndices = Array.from(Array(questions.length).keys());
            const numberOfPrizes = Math.min(6, questions.length);
            for (let i = 0; i < numberOfPrizes; i++) {
                if (availableIndices.length === 0) break;
                const randomIndex = Math.floor(Math.random() * availableIndices.length);
                const questionIndex = availableIndices.splice(randomIndex, 1)[0];
                prizeLocations[questionIndex] = prizes[i];
            }
        }

        function setupTeams() {
            const numTeamsInput = document.getElementById("num-teams");
            const teamNamesInputsDiv = document.getElementById("team-names-inputs");
            const numTeams = parseInt(numTeamsInput.value);

            if (isNaN(numTeams) || numTeams <= 0) {
                alert("الرجاء إدخال عدد صحيح موجب للفرق.");
                return;
            }

            teamNamesInputsDiv.innerHTML = "";
            teams = [];
            for (let i = 0; i < numTeams; i++) {
                let input = document.createElement("input");
                input.type = "text";
                input.placeholder = `اسم الفريق ${i + 1}`;
                input.id = `team-name-${i}`;
                teamNamesInputsDiv.appendChild(input);
                teams.push("");
            }

            let setupButton = document.createElement("button");
            setupButton.innerText = "تأكيد الفرق";
            setupButton.onclick = () => {
                let valid = true;
                for (let i = 0; i < numTeams; i++) {
                    const teamNameInput = document.getElementById(`team-name-${i}`);
                    if (!teamNameInput.value.trim()) {
                        alert(`الرجاء إدخال اسم صحيح للفريق ${i + 1}`);
                        valid = false;
                        break;
                    }
                    teams[i] = teamNameInput.value.trim();
                }
                if (valid) {
                    score = {};
                    teams.forEach(team => {
                        score[team] = 0;
                    });
                    updateAdminTeamScoreDropdown();
                    updateAdminTeamExcludeDropdown(); // تحديث قائمة استبعاد الفريق
                    document.getElementById("team-setup").style.display = "none";
                    document.getElementById("team-selection").style.display = "block";
                    document.getElementById("questionButtons").style.display = "block";
                    document.getElementById("questionText").style.display = "none"; // إخفاء نص السؤال في البداية
                    document.getElementById("options").style.display = "none"; // إخفاء الخيارات في البداية
                    document.getElementById("timer").style.display = "block";
                    document.getElementById("result").style.display = "block";
                    document.getElementById("nextButton").style.display = "none";
                    generateQuestionButtons();
                    updateTeamScoreDisplay();
                    updateTeamScoreDisplay();
                    generateTeamSelection(); // <--- نقل هذا السطر إلى هنا
                }
            };
            teamNamesInputsDiv.appendChild(setupButton);
        }

        function generateTeamSelection() {
            let teamSelectionDiv = document.getElementById("team-selection");
            if (!teamSelectionDiv) {
                console.error("Error: team-selection div is null in generateTeamSelection");
                return;
            }
            teamSelectionDiv.innerHTML = "<p>اختر الفريق:</p>";
            teams.forEach(team => {
                const isExcluded = excludedTeams.includes(team);
                let teamButton = document.createElement("button");
                teamButton.innerText = team;
                if (isExcluded) {
                    teamButton.classList.add("excluded-team");
                    teamButton.disabled = true; // تعطيل الزر إذا كان الفريق مستبعدًا
                } else {
                    teamButton.onclick = () => {
                        currentTeam = team;
                        alert(`تم اختيار الفريق: ${team}`);
                        document.getElementById("startTimer").style.display = "block";
                    };
                }
                teamSelectionDiv.appendChild(teamButton);
            });
        }

        function generateQuestionButtons() {
            let buttonsHtml = "";
            for (let i = 0; i < questions.length; i++) {
                buttonsHtml += `<button onclick="displayQuestion(${i})" id="question-button-${i}">${i + 1}</button>`;
            }
            document.getElementById("questionButtons").innerHTML = buttonsHtml;
        }

       function displayQuestion(index) {
            if (!currentTeam) {
                alert("الرجاء اختيار الفريق أولاً.");
                return;
            }
            if (excludedTeams.includes(currentTeam)) {
                alert(`لا يمكن لفريق ${currentTeam} المشاركة لأنه مستبعد.`);
                return;
            }
            if (selectedQuestions.includes(index)) {
                alert("تم عرض هذا السؤال بالفعل.");
                return;
            }

            clearInterval(countdown); // مسح أي مؤقت قد يكون قيد التشغيل قبل عرض السؤال الجديد
            document.getElementById("timer").innerText = ""; // إعادة تعيين عرض المؤقت

            currentQuestionIndex = index;
            const resultElement = document.getElementById("result");
            const questionButton = document.getElementById(`question-button-${index}`);

            if (prizeLocations.hasOwnProperty(index)) {
                const prize = prizeLocations[index];
                score[currentTeam]++;
                resultElement.innerText = `🎉 لقد فاز فريق ${currentTeam} بـ: ${prize}! وتم إضافة نقطة. 🎉`;
                resultElement.className = "correct";
                resultElement.style.display = "block";
                questionButton.classList.add("prize-obtained"); // إضافة الكلاس الجديد لتمييز زر الجائزة
                selectedQuestions.push(index);
                updateTeamScoreDisplay();
                document.getElementById("questionText").style.display = "none";
                document.getElementById("options").style.display = "none";
                document.getElementById("timer").style.display = "none";
                document.getElementById("startTimer").style.display = "none";
                document.getElementById("nextButton").style.display = "block";
            } else {
                const currentQuestion = questions[index];
                document.getElementById("questionText").innerText = currentQuestion.question;
                document.getElementById("options").innerHTML = "";
                currentQuestion.options.forEach((option, i) => {
                    const button = document.createElement("button");
                    button.innerText = option;
                    button.onclick = () => checkAnswer(i, index);
                    document.getElementById("options").appendChild(button);
                });
                document.getElementById("questionText").style.display = "block";
                document.getElementById("options").style.display = "block";
                document.getElementById("timer").style.display = "block"; // إظهار المؤقت (سيتم تحديث النص عند بدء العد)
                document.getElementById("startTimer").style.display = "block"; // إظهار زر بدء المؤقت
                document.getElementById("result").style.display = "none";
                document.getElementById("nextButton").style.display = "none";
                questionButton.classList.add("selected");
                selectedQuestions.push(index);
                answered = false;
            }
        }

        function checkAnswer(selectedIndex, questionIndex) {
            if (answered) return;
            answered = true;
            const currentQuestion = questions[questionIndex];
            const resultElement = document.getElementById("result");
            const questionButton = document.getElementById(`question-button-${questionIndex}`);

            if (selectedIndex === currentQuestion.answer) {
                score[currentTeam]++;
                resultElement.innerText = "إجابة صحيحة! 🎉";
                resultElement.className = "correct";
                questionButton.classList.add("correct");
            } else {
                resultElement.innerText = `إجابة خاطئة. الإجابة الصحيحة هي: ${currentQuestion.options[currentQuestion.answer]}`;
                resultElement.className = "incorrect";
                questionButton.classList.add("incorrect");
            }
            resultElement.style.display = "block";
            updateTeamScoreDisplay();
            document.getElementById("questionText").style.display = "none";
            document.getElementById("options").style.display = "none";
            document.getElementById("timer").style.display = "none";
            document.getElementById("startTimer").style.display = "none";
            document.getElementById("nextButton").style.display = "block";
        }

        function nextQuestion() {
            document.getElementById("questionText").style.display = "none";
            document.getElementById("options").style.display = "none";
            document.getElementById("result").style.display = "none";
            document.getElementById("nextButton").style.display = "none";
            document.getElementById("startTimer").style.display = "block";
            currentTeam = null;
            generateTeamSelection(); // إعادة عرض اختيار الفريق
        }

        function startCountdown() {
            let seconds = 9;
            clearInterval(countdown); // مسح أي مؤقت سابق
            document.getElementById("timer").innerText = `الوقت المتبقي: ${seconds}`;
            document.getElementById("startTimer").style.display = "none";
            countdown = setInterval(() => {
                seconds--;
                document.getElementById("timer").innerText = `الوقت المتبقي: ${seconds}`;
                if (seconds <= 0) {
                    clearInterval(countdown);
                    document.getElementById("timer").innerText = "انتهى الوقت!";
                    const currentQuestion = questions[currentQuestionIndex];
                    const correctAnswer = currentQuestion.options[currentQuestion.answer];
                    document.getElementById("result").innerText = `انتهى الوقت! الإجابة الصحيحة هي: ${correctAnswer}`;
                    document.getElementById("result").className = "incorrect"; // يمكنك تغيير الكلاس إذا أردت تمييزها بشكل مختلف
                    document.getElementById("result").style.display = "block";
                    document.getElementById("questionText").style.display = "none";
                    document.getElementById("options").style.display = "none";
                    document.getElementById("nextButton").style.display = "block";
                }
            }, 1000);
        }

        function updateTeamScoreDisplay() {
            const teamSelectionDiv = document.getElementById("team-selection");
            if (teamSelectionDiv) {
                const scoreElements = teamSelectionDiv.querySelectorAll('.team-score');
                scoreElements.forEach(element => element.remove());

                for (const team in score) {
                    const scoreElement = document.createElement('p');
                    scoreElement.className = 'team-score';
                    scoreElement.innerText = `${team}: ${score[team]} نقطة`;
                    const teamButton = Array.from(teamSelectionDiv.children).find(child => child.innerText === team && child.tagName === 'BUTTON');
                    if (teamButton) {
                        teamButton.insertAdjacentElement('afterend', scoreElement);
                    } else {
                        teamSelectionDiv.appendChild(scoreElement);
                    }
                }
            }
        }

        function updateAdminTeamScoreDropdown() {
            const selectTeamScore = document.getElementById("select-team-score");
            selectTeamScore.innerHTML = "";
            teams.forEach(team => {
                const option = document.createElement("option");
                option.value = team;
                option.innerText = team;
                selectTeamScore.appendChild(option);
            });
        }

        function incrementScore() {
            const selectTeam = document.getElementById("select-team-score");
            const selectedTeam = selectTeam.value;
            if (score.hasOwnProperty(selectedTeam)) {
                score[selectedTeam]++;
                updateTeamScoreDisplay();
            }
        }

        function decrementScore() {
            const selectTeam = document.getElementById("select-team-score");
            const selectedTeam = selectTeam.value;
            if (score.hasOwnProperty(selectedTeam) && score[selectedTeam] > 0) {
                score[selectedTeam]--;
                updateTeamScoreDisplay();
            }
        }

        function setScore() {
            const selectTeam = document.getElementById("select-team-score");
            const scoreValueInput = document.getElementById("set-score-value");
            const selectedTeam = selectTeam.value;
            const newScore = parseInt(scoreValueInput.value);
            if (!isNaN(newScore) && score.hasOwnProperty(selectedTeam)) {
                score[selectedTeam] = Math.max(0, newScore);
                updateTeamScoreDisplay();
                scoreValueInput.value = "";
            } else {
                alert("الرجاء إدخال قيمة صحيحة للنقاط.");
            }
        }

        function addTeam() {
            const newTeamNameInput = document.getElementById("new-team-name");
            const newTeamName = newTeamNameInput.value.trim();
            if (newTeamName && !teams.includes(newTeamName)) {
                teams.push(newTeamName);
                score[newTeamName] = 0;
                updateAdminTeamScoreDropdown();
                updateAdminTeamExcludeDropdown(); // تحديث قائمة الاستبعاد عند إضافة فريق
                updateTeamScoreDisplay();
                generateTeamSelection(); // تحديث قائمة اختيار الفريق
                newTeamNameInput.value = "";
            } else if (teams.includes(newTeamName)) {
                alert("هذا الفريق موجود بالفعل.");
            } else {
                alert("الرجاء إدخال اسم للفريق الجديد.");
            }
        }

        function updateAdminTeamExcludeDropdown() {
            const selectTeamExclude = document.getElementById("select-team-exclude");
            selectTeamExclude.innerHTML = "";
            teams.forEach(team => {
                const option = document.createElement("option");
                option.value = team;
                option.innerText = team;
                selectTeamExclude.appendChild(option);
            });
        }

        function excludeTeam() {
            const selectTeam = document.getElementById("select-team-exclude");
            const teamToExclude = selectTeam.value;
            if (teamToExclude && !excludedTeams.includes(teamToExclude)) {
                excludedTeams.push(teamToExclude);
                alert(`تم استبعاد فريق ${teamToExclude}.`);
                generateTeamSelection(); // إعادة عرض قائمة الفرق مع تحديث الحالة
            } else if (excludedTeams.includes(teamToExclude)) {
                alert(`فريق ${teamToExclude} مستبعد بالفعل.`);
            } else {
                alert("الرجاء اختيار فريق للاستبعاد.");
            }
        }

        function includeTeam() {
            const selectTeam = document.getElementById("select-team-exclude");
            const teamToInclude = selectTeam.value;
            const index = excludedTeams.indexOf(teamToInclude);
            if (index > -1) {
                excludedTeams.splice(index, 1);
                alert(`تمت إعادة فريق ${teamToInclude} إلى المسابقة.`);
                generateTeamSelection(); // إعادة عرض قائمة الفرق مع تحديث الحالة
            } else if (teamToInclude && !teams.includes(teamToInclude)) {
                alert("هذا الفريق غير موجود.");
            } else {
                alert(`فريق ${teamToInclude} ليس مستبعدًا.`);
            }
        }

        initializeGame();
    </script>
</body>
</html>
