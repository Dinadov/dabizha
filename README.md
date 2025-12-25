<!DOCTYPE html>
<html lang="uk">
<head>
<meta charset="UTF-8">
<title>Кінотеатр Бєляєвка</title>
<style>
    body {
        font-family: 'Arial', sans-serif;
        background: linear-gradient(to bottom, #f0f0f0, #d0e7ff);
        margin: 0; padding: 0;
    }
    header {
        background-color: #1e90ff;
        color: white;
        padding: 20px;
        text-align: center;
    }
    h1 { margin: 0; }
    main { padding: 20px; max-width: 900px; margin: auto; }
    .film {
        background: #fff;
        padding: 15px;
        margin: 10px 0;
        border-radius: 8px;
        box-shadow: 0 0 5px rgba(0,0,0,0.2);
    }
    select, button {
        padding: 5px 10px;
        margin: 5px 0;
    }
    #seatMap {
        display: grid;
        grid-template-columns: repeat(8, 40px);
        gap: 5px;
        margin-top: 10px;
    }
    .seat {
        width: 40px; height: 40px;
        background: #ccc;
        border-radius: 5px;
        cursor: pointer;
        text-align: center; line-height: 40px;
    }
    .seat.selected { background: #1e90ff; color: white; }
    #cart {
        background: #fff;
        padding: 15px;
        border-radius: 8px;
        margin-top: 20px;
        box-shadow: 0 0 5px rgba(0,0,0,0.2);
    }
</style>
</head>
<body>
<header>
    <h1>Кінотеатр Бєляєвка</h1>
</header>
<main>
    <div id="films"></div>
    <div id="cart">
        <h2>Ваші квитки:</h2>
        <ul id="ticketList"></ul>
        <strong>Загальна сума: $<span id="total">0</span></strong>
    </div>
</main>

<script>
const films = [
    { id: 1, title: "Матриця", price: 50, sessions: ["2025-12-26 14:00", "2025-12-26 18:00"] },
    { id: 2, title: "Інтерстеллар", price: 60, sessions: ["2025-12-26 15:00", "2025-12-26 19:00"] },
    { id: 3, title: "Дюна", price: 55, sessions: ["2025-12-26 16:00", "2025-12-26 20:00"] },
    { id: 4, title: "Аватар 2", price: 70, sessions: ["2025-12-26 17:00", "2025-12-26 21:00"] }
];

let cart = [];

function renderFilms() {
    const filmsDiv = document.getElementById("films");
    filmsDiv.innerHTML = "";
    films.forEach(film => {
        const filmDiv = document.createElement("div");
        filmDiv.className = "film";

        // Сесії
        let sessionOptions = film.sessions.map(s => `<option value="${s}">${s}</option>`).join('');

        filmDiv.innerHTML = `
            <h3>${film.title}</h3>
            <p>Ціна квитка: $${film.price}</p>
            <label>Оберіть сеанс:</label>
            <select id="session-${film.id}">${sessionOptions}</select>
            <div id="seatMap-${film.id}" class="seatMap"></div>
            <button onclick="buyTicket(${film.id})">Купити обране місце</button>
        `;
        filmsDiv.appendChild(filmDiv);

        renderSeats(film.id);
    });
}

function renderSeats(filmId) {
    const seatMap = document.getElementById(`seatMap-${filmId}`);
    seatMap.innerHTML = "";
    for(let i=1;i<=24;i++){
        const seat = document.createElement("div");
        seat.className = "seat";
        seat.textContent = i;
        seat.onclick = () => seat.classList.toggle("selected");
        seatMap.appendChild(seat);
    }
}

function buyTicket(filmId){
    const film = films.find(f => f.id === filmId);
    const session = document.getElementById(`session-${filmId}`).value;
    const seats = Array.from(document.getElementById(`seatMap-${filmId}`).children)
        .filter(s => s.classList.contains("selected"))
        .map(s => s.textContent);

    if(seats.length === 0){ alert("Оберіть місце!"); return; }

    seats.forEach(seat => {
        cart.push({film: film.title, session, seat, price: film.price});
    });

    renderCart();

    // Зняти виділення місць
    Array.from(document.getElementById(`seatMap-${filmId}`).children)
        .forEach(s => s.classList.remove("selected"));
}

function renderCart(){
    const ticketList = document.getElementById("ticketList");
    ticketList.innerHTML = "";
    cart.forEach(ticket => {
        const li = document.createElement("li");
        li.textContent = `${ticket.film} | ${ticket.session} | Місце: ${ticket.seat} | $${ticket.price}`;
        ticketList.appendChild(li);
    });
    const total = cart.reduce((sum,t)=>sum+t.price,0);
    document.getElementById("total").textContent = total;
}

renderFilms();
</script>
</body>
</html>
