<script>
const board = document.getElementById("board");

function loadTimetable() {
  fetch("./hakata.json")
    .then(res => res.json())
    .then(data => {
      board.innerHTML = "";

      const now = new Date();
      const nowMinutes = now.getHours() * 60 + now.getMinutes();

      data.forEach(t => {
        const [h, m] = t.time.split(":").map(Number);
        const trainMinutes = h * 60 + m;

        // 過去列車は非表示
        if (trainMinutes < nowMinutes) return;

        const row = document.createElement("div");
        row.className = "row";

        const typeClass = {
          "のぞみ": "nozomi",
          "ひかり": "hikari",
          "こだま": "kodama",
          "さくら": "sakura",
          "みずほ": "mizuho",
          "つばめ": "tsubame"
        }[t.type] || "";

        row.innerHTML = `
          <div class="top">
            <div class="train ${typeClass}">
              ${t.type} ${t.number}号
            </div>
            <div class="time">${t.time}</div>
          </div>
          <div class="dest">${t.destination}</div>
        `;

        board.appendChild(row);
      });

      // 🔽 ここが⑨の本体
      board.scrollTo({
        top: 0,
        behavior: "smooth"
      });
    })
    .catch(() => {
      board.innerHTML = "ダイヤを読み込めません";
    });
}

// 🔁 30秒ごとに更新（おすすめ）
loadTimetable();
setInterval(loadTimetable, 30000);
</script>