# my-coquette-calender-
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Coquette Calendar</title>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

<style>
    *{
        margin:0;
        padding:0;
        box-sizing:border-box;
    }

    body{
        min-height:100vh;
        display:flex;
        justify-content:center;
        align-items:center;
        background:
            radial-gradient(circle at top, #ffe6f0, #ffd6ea, #fff0f5);
        font-family: "Georgia", serif;
        overflow:hidden;
    }

    .sparkles{
        position:absolute;
        inset:0;
        pointer-events:none;
        background-image:
            radial-gradient(#ffffff 1px, transparent 1px);
        background-size:40px 40px;
        opacity:0.4;
        animation:float 15s linear infinite;
    }

    @keyframes float{
        from{transform:translateY(0);}
        to{transform:translateY(-40px);}
    }

    .calendar{
        width:380px;
        background:rgba(255,255,255,0.35);
        backdrop-filter: blur(14px);
        border:2px solid rgba(255,255,255,0.5);
        border-radius:32px;
        padding:24px;
        box-shadow:
            0 8px 32px rgba(255, 170, 200, 0.35);
        position:relative;
    }

    .ribbon{
        position:absolute;
        top:-18px;
        right:20px;
        background:#ffb6cf;
        color:white;
        padding:8px 18px;
        border-radius:20px;
        font-size:13px;
        letter-spacing:1px;
        box-shadow:0 4px 12px rgba(255,182,207,.5);
    }

    .header{
        display:flex;
        justify-content:space-between;
        align-items:center;
        margin-bottom:25px;
    }

    .month{
        font-size:28px;
        color:#c25a7c;
        font-weight:bold;
    }

    .btn{
        width:42px;
        height:42px;
        border:none;
        border-radius:50%;
        background:#ffd6e7;
        color:#c25a7c;
        font-size:20px;
        cursor:pointer;
        transition:0.3s ease;
        box-shadow:0 4px 10px rgba(255,182,193,0.4);
    }

    .btn:hover{
        transform:scale(1.1);
        background:#ffc2da;
    }

    .weekdays{
        display:grid;
        grid-template-columns:repeat(7,1fr);
        text-align:center;
        margin-bottom:14px;
        color:#c47a95;
        font-size:14px;
        font-weight:bold;
    }

    .days{
        display:grid;
        grid-template-columns:repeat(7,1fr);
        gap:10px;
    }

    .day{
        aspect-ratio:1/1;
        border-radius:18px;
        display:flex;
        justify-content:center;
        align-items:center;
        cursor:pointer;
        background:rgba(255,255,255,0.45);
        color:#b55d80;
        font-size:16px;
        transition:0.25s ease;
        position:relative;
        user-select:none;
    }

    .day:hover{
        transform:translateY(-4px) scale(1.05);
        background:#ffd3e4;
        box-shadow:0 6px 14px rgba(255,182,193,.45);
    }

    .day.active{
        background:linear-gradient(135deg,#ffb6cf,#ff8fb4);
        color:white;
        box-shadow:0 8px 18px rgba(255,143,180,.5);
    }

    .day.active::after{
        content:"â¡";
        position:absolute;
        bottom:5px;
        font-size:10px;
    }

    .note{
        margin-top:22px;
        padding:14px;
        border-radius:18px;
        background:rgba(255,255,255,.4);
        color:#b36a87;
        text-align:center;
        font-size:14px;
        min-height:50px;
    }

    .heart{
        position:absolute;
        color:#ffb3cb;
        font-size:18px;
        animation:drift 6s linear infinite;
        opacity:.5;
    }

    @keyframes drift{
        0%{
            transform:translateY(0) rotate(0deg);
            opacity:0;
        }
        20%{
            opacity:.6;
        }
        100%{
            transform:translateY(-100vh) rotate(360deg);
            opacity:0;
        }
    }
</style>
</head>

<body>

<div class="sparkles"></div>

<div class="calendar">
    <div class="ribbon">coquette diary â¿</div>

    <div class="header">
        <button class="btn" id="prev">â</button>
        <div class="month" id="monthYear"></div>
        <button class="btn" id="next">â</button>
    </div>

    <div class="weekdays">
        <div>Sun</div>
        <div>Mon</div>
        <div>Tue</div>
        <div>Wed</div>
        <div>Thu</div>
        <div>Fri</div>
        <div>Sat</div>
    </div>

    <div class="days" id="days"></div>

    <div class="note" id="note">
        Click a date to add your dreamy plans â¡
    </div>
</div>

<script>
    const monthYear = document.getElementById("monthYear");
    const daysContainer = document.getElementById("days");
    const note = document.getElementById("note");

    const months = [
        "January","February","March","April","May","June",
        "July","August","September","October","November","December"
    ];

    let date = new Date();

    function renderCalendar() {
        daysContainer.innerHTML = "";

        const year = date.getFullYear();
        const month = date.getMonth();

        const firstDay = new Date(year, month, 1).getDay();
        const totalDays = new Date(year, month + 1, 0).getDate();

        monthYear.innerText = `${months[month]} ${year}`;

        for(let i = 0; i < firstDay; i++){
            const empty = document.createElement("div");
            daysContainer.appendChild(empty);
        }

        for(let day = 1; day <= totalDays; day++){
            const dayEl = document.createElement("div");
            dayEl.classList.add("day");
            dayEl.innerText = day;

            dayEl.addEventListener("click", () => {
                document.querySelectorAll(".day").forEach(d => {
                    d.classList.remove("active");
                });

                dayEl.classList.add("active");

                let saved = localStorage.getItem(`${year}-${month}-${day}`) || "";

                let text = prompt(
                    `Enter your coquette plans for ${day} ${months[month]} â¿`,
                    saved
                );

                if(text !== null){
                    localStorage.setItem(`${year}-${month}-${day}`, text);
                    note.innerHTML = `
                        <b>${day} ${months[month]}</b><br>
                        ${text || "No notes yet â¡"}
                    `;
                }
            });

            daysContainer.appendChild(dayEl);
        }
    }

    document.getElementById("prev").onclick = () => {
        date.setMonth(date.getMonth() - 1);
        renderCalendar();
    };

    document.getElementById("next").onclick = () => {
        date.setMonth(date.getMonth() + 1);
        renderCalendar();
    };

    renderCalendar();

    // floating hearts
    function createHeart(){
        const heart = document.createElement("div");
        heart.classList.add("heart");
        heart.innerHTML = "â¡";

        heart.style.left = Math.random() * window.innerWidth + "px";
        heart.style.top = window.innerHeight + "px";
        heart.style.fontSize = (12 + Math.random()*20) + "px";

        document.body.appendChild(heart);

        setTimeout(() => {
            heart.remove();
        }, 6000);
    }

    setInterval(createHeart, 700);
</script>

</body>
</html>