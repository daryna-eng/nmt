<html lang="uk">
<head>
  <meta charset="UTF-8" />
  <title>Підбір університету за НМТ</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      background: #f4f7fa;
    }
    .header {
      background: #003366;
      color: white;
      padding: 20px;
      text-align: center;
    }
    .container {
      max-width: 1200px;
      margin: 20px auto;
      background: white;
      padding: 20px;
      border-radius: 10px;
    }
    input, button {
      padding: 10px;
      font-size: 16px;
      width: 100%;
      margin-top: 10px;
    }
    button {
      background: #004080;
      color: white;
      border: none;
    }
    .university {
      background: #e6f0ff;
      margin-top: 20px;
      padding: 15px;
      border-radius: 8px;
      display: flex;
      gap: 20px;
      flex-wrap: wrap;
    }
    .university img {
      width: 250px;
      height: 170px;
      object-fit: cover;
      border-radius: 6px;
    }
    .info {
      flex: 1;
      min-width: 250px;
    }
    #map {
      height: 400px;
      margin-top: 20px;
      border-radius: 8px;
      display: none;
    }
  </style>

  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
</head>
<body>

<div class="header">
  <h1>Знайди університет за своїм балом НМТ</h1>
</div>

<div class="container">
  <label>Введи свій середній бал НМТ:</label>
  <input type="number" id="scoreInput" placeholder="від 100 до 200" />
  <button onclick="findUniversities()">Знайти університети</button>

  <div id="map"></div>
  <div id="results"></div>
</div>

<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
<script>
const universities = [
  {
    name: "КНУ імені Тараса Шевченка",
    faculty: "Комп’ютерні науки",
    minScore: 188,
    contractScore: 165,
    statePlaces: 90,
    description: "Один з провідних університетів України, заснований у 1834 році.",
    alumni: ["Оксана Забужко", "Леонід Кучма"],
    image: "https://scontent.fiev22-1.fna.fbcdn.net/v/t39.30808-6/382241510_723954793107215_3669528546435362377_n.jpg?_nc_cat=106&ccb=1-7&_nc_sid=6ee11a&_nc_ohc=NBozArZgPscQ7kNvwGcWWeK&_nc_oc=AdmtAmnmRFzQK4ClQVlHCBpDEgGMbRqNQnPCgJzk4oIg1ESIxh9mShrhdqkbGWBEUOY&_nc_zt=23&_nc_ht=scontent.fiev22-1.fna&_nc_gid=CxcCl6r7hliy5_vQdTcQHw&oh=00_AfGE2vgFhZzWh97O40K3weG_Y-eDVpgoKBOqPA0DN1U2OQ&oe=681B92E2",
    lat: 50.4368,
    lon: 30.5197
  },
  {
    name: "Київський політехнічний інститут (КПІ)",
    faculty: "Кібербезпека",
    minScore: 183,
    contractScore: 160,
    statePlaces: 75,
    description: "Провідний технічний університет України.",
    alumni: ["Ігор Сікорський"],
    image: "https://vandrivka.com.ua/wp-content/uploads/2018/11/Kiyivskij-politehnichnij-institut-imeni-Igorya-Sikorskogo-Kiyiv-5.jpg",
    lat: 50.4501,
    lon: 30.4565
  },
  {
    name: "Львівський національний університет",
    faculty: "Правознавство",
    minScore: 181,
    contractScore: 158,
    statePlaces: 60,
    description: "Один з найстаріших університетів України.",
    alumni: ["Іван Франко", "Станіслав Лем"],
    image: "https://api.buki.com.ua/news_image/BY/jC/BYjCF3FMzSb0G5JB2c5meiXKSpuBsZu2mfFGL8Wr.jpg",
    lat: 49.8419,
    lon: 24.0315
  },
  {
    name: "Національний авіаційний університет (НАУ)",
    faculty: "Авіаційна безпека",
    minScore: 172,
    contractScore: 150,
    statePlaces: 30,
    description: "Спеціалізується на авіаційних спеціальностях.",
    alumni: ["Анатолій Загородній"],
    image: "https://mistokyia.ua/wp-content/uploads/2023/06/nacionalnyj-aviacijnyj-universytet-1-1130x650.jpg",
    lat: 50.4120,
    lon: 30.4511
  },
  {
  name: "Волинський національний університет імені Лесі Українки",
  faculty: "Філологія",
  minScore: 155,
  contractScore: 135,
  statePlaces: 40,
  description: "Один з провідних освітніх закладів Волині з широким гуманітарним профілем.",
  alumni: ["Ірина Констанкевич"],
  image: "https://upload.wikimedia.org/wikipedia/commons/8/88/Lutsk_Univercity.jpg",
  lat: 50.7472,
  lon: 25.3254
   },
  {
  name: "Одеський національний університет імені І. І. Мечникова",
  faculty: "Біологія",
  minScore: 162,
  contractScore: 140,
  statePlaces: 50,
  description: "Один з найстаріших університетів півдня України, знаний своїми науковими школами.",
  alumni: ["Ілля Ільф"],
  image: "https://yug.today/wp-content/uploads/2025/02/Odeskyy-natsionalnyy-universytet-imeni-I.-I.-Mechnykova-e1680763638662-960x640.jpg",
  lat: 46.4775,
  lon: 30.7326
    },
  {
  name: "Чернівецький національний університет імені Ю. Федьковича",
  faculty: "Історія",
  minScore: 160,
  contractScore: 140,
  statePlaces: 40,
  description: "Унікальна архітектурна пам’ятка та потужний центр освіти Буковини.",
  alumni: ["Юрій Федькович"],
  image: "https://bukoda.gov.ua/storage/app/uploads/public/613/1c8/29b/6131c829b4b5a220544989.png",
  lat: 48.2915,
  lon: 25.9358
    },
  {
  name: "Сумський державний університет",
  faculty: "Комп’ютерна інженерія",
  minScore: 165,
  contractScore: 145,
  statePlaces: 60,
  description: "Інноваційний університет північного сходу України.",
  alumni: [],
  image: "https://med.sumdu.edu.ua/wp-content/uploads/2022/06/IMG_20220524_113150_1m.jpg",
  lat: 50.9087,
  lon: 34.8135
    },
  {
  name: "Ужгородський національний університет",
  faculty: "Медицина",
  minScore: 179,
  contractScore: 160,
  statePlaces: 70,
  description: "Найзахідніший університет України з високим рівнем медичної освіти.",
  alumni: [],
  image: "https://www.uzhnu.edu.ua/uploads/root/content/foreign_stud/uzhnu1.jpg",
  lat: 48.6208,
  lon: 22.2879
    },
 {
  name: "Тернопільський національний економічний університет",
  faculty: "Облік і аудит",
  minScore: 150,
  contractScore: 130,
  statePlaces: 45,
  description: "Потужна економічна школа західного регіону України.",
  alumni: ["Олег Барна"],
  image: "https://tv4.te.ua/wp-content/webp-express/webp-images/uploads/2020/08/vlcsnap-2020-08-26-16h38m32s207.jpg.webp",
  lat: 49.5535,
  lon: 25.5948
    },
  {
   name: "Полтавський національний технічний університет",
  faculty: "Будівництво",
  minScore: 148,
  contractScore: 130,
  statePlaces: 35,
  description: "Технічний виш з багаторічними традиціями та інженерними спеціальностями.",
  alumni: [],
  image: "https://i1.poltava.to/uploads/2015/03/2015-03-07/poltntu.jpg",
  lat: 49.5927,
  lon: 34.5466
     },
  {
  name: "Прикарпатський національний університет імені Василя Стефаника",
  faculty: "Психологія",
  minScore: 158,
  contractScore: 138,
  statePlaces: 50,
  description: "Головний освітній осередок Івано-Франківської області.",
  alumni: [],
  image: "https://www.jta.com.ua/wp-content/uploads/2023/09/Prykarpatskyy.jpg",
  lat: 48.9226,
  lon: 24.7103
     },
  {
  name: "Дніпровський національний університет імені Олеся Гончара",
  faculty: "Фізика",
  minScore: 163,
  contractScore: 143,
  statePlaces: 40,
  description: "Центральний університет Придніпров’я з потужними технічними напрямками.",
  alumni: ["Олесь Гончар"],
  image: "https://www.dnu.dp.ua/images/VEL03258.jpg",
  lat: 48.4333,
  lon: 35.0427
     },
  {
   name: "Херсонський державний університет",
  faculty: "Початкова освіта",
  minScore: 145,
  contractScore: 125,
  statePlaces: 25,
  description: "Один з найстаріших педагогічних вишів півдня України.",
  alumni: [],
  image: "https://kherson.rayon.in.ua/storage/cache/images/upload/news/40/2023-12/14-Yg7W2BGh/700x371-657acecb7957f.webp",
  lat: 46.6426,
  lon: 32.6256
     },
  {
  name: "Вінницький національний технічний університет",
  faculty: "Інженерія програмного забезпечення",
  minScore: 167,
  contractScore: 145,
  statePlaces: 60,
  description: "Технічний університет з сильними IT-напрямами в центрі України.",
  alumni: [],
  image: "https://scontent.fiev22-1.fna.fbcdn.net/v/t39.30808-6/475655492_1012135184292197_3076001674050549021_n.jpg?_nc_cat=103&ccb=1-7&_nc_sid=cc71e4&_nc_ohc=mev_F92F9dUQ7kNvwE4Ecc7&_nc_oc=AdmVTwl7a3CGtkx4iTo0uDu2x7DfZOegJTDmrywPVsI-2VN6O_geVZ39g15CIqkVRXA&_nc_zt=23&_nc_ht=scontent.fiev22-1.fna&_nc_gid=ZY9oichq1SemMS_SxkbdoA&oh=00_AfGvDG_VRpIBDhWt_KlPdk_SG-wukt1oTqKxcBzoznUsoQ&oe=681B87D9",
  lat: 49.2331,
  lon: 28.4682
     },
  {
  name: "Луцький національний технічний університет",
  faculty: "Електроніка",
  minScore: 153,
  contractScore: 132,
  statePlaces: 40,
  description: "Профільний технічний виш західного регіону.",
  alumni: [],
  image: "https://www.volynnews.com/files/news/2018/05-17/244920/54651aaaa.jpg",
  lat: 50.7375,
  lon: 25.3256
     },
  {
   name: "Кременчуцький національний університет імені Михайла Остроградського",
  faculty: "Автоматизація та комп’ютерно-інтегровані технології",
  minScore: 149,
  contractScore: 128,
  statePlaces: 30,
  description: "Відома інженерна школа на Полтавщині.",
  alumni: [],
  image: "https://euroosvita.net/prog/data/upimages/1korpus_krnu.jpg",
  lat: 49.0746,
  lon: 33.4184
     },
  {
   name: "Донбаська державна машинобудівна академія",
  faculty: "Машинобудування",
  minScore: 146,
  contractScore: 125,
  statePlaces: 35,
  description: "Профільний технічний заклад Донеччини.",
  alumni: [],
  image: "http://www.dgma.donetsk.ua/science_public/science_vesnik/TITUL_files/building.jpg",
  lat: 48.7208,
  lon: 37.5563
     },
  {
   name: "Ізмаїльський державний гуманітарний університет",
  faculty: "Філологія (українська мова)",
  minScore: 141,
  contractScore: 120,
  statePlaces: 25,
  description: "Заклад на півдні Одеської області з гуманітарною спеціалізацією.",
  alumni: [],
  image: "https://st2.prosto.im/cache/st2/1/0/4/1/1041552/1041552_230x.jpg",
  lat: 45.3516,
  lon: 28.8292
     },
  {
  name: "Кам’янець-Подільський національний університет імені Івана Огієнка",
  faculty: "Історія та археологія",
  minScore: 150,
  contractScore: 130,
  statePlaces: 40,
  description: "Педагогічний і науковий центр Хмельниччини.",
  alumni: [],
  image: "https://euroosvita.net/prog/data/upimages/k_pnu-adm.jpg",
  lat: 48.6836,
  lon: 26.5842
     },
  {
  name: "Кропивницький національний технічний університет",
  faculty: "Галузеве машинобудування",
  minScore: 144,
  contractScore: 122,
  statePlaces: 30,
  description: "Центральний технічний виш Кіровоградщини.",
  alumni: [],
  image: "https://scontent.fiev22-1.fna.fbcdn.net/v/t39.30808-6/239508408_4161318560650529_2554485298384177279_n.jpg?_nc_cat=105&ccb=1-7&_nc_sid=cc71e4&_nc_ohc=UA9xXizGHu8Q7kNvwF2s0rK&_nc_oc=AdmNYVuM9rjKWbgeL-JO68vvQH2JZ5wlLCuvfY7kEAEqu7OlVuNsRUQQUb-TJG-QtAc&_nc_zt=23&_nc_ht=scontent.fiev22-1.fna&_nc_gid=lr5cIqnZXB8uwLSQsRLGfg&oh=00_AfFqHiSK5dVhfwXc06Hoa6agKFCf9uJ9va_irsv1HeIs_A&oe=681B931D",
  lat: 48.5062,
  lon: 32.2623
     },
  {
  name: "Рівненський державний гуманітарний університет",
  faculty: "Дошкільна освіта",
  minScore: 142,
  contractScore: 120,
  statePlaces: 28,
  description: "Педагогічний виш з давніми традиціями на Рівненщині.",
  alumni: [],
  image: "https://www.rshu.edu.ua/images/djmediatools/1-front/header.jpg",
  lat: 50.6199,
  lon: 26.2516
     },
  {
   name: "Черкаський національний університет імені Богдана Хмельницького",
  faculty: "Математика",
  minScore: 151,
  contractScore: 130,
  statePlaces: 35,
  description: "Головний університет Черкащини.",
  alumni: [],
  image: "https://provce.ck.ua/wp-content/uploads/2025/01/photo_2025-01-06_10-15-37.jpg",
  lat: 49.4285,
  lon: 32.0621
    },
  {
  name: "Бердянський державний педагогічний університет",
  faculty: "Середня освіта (математика)",
  minScore: 143,
  contractScore: 122,
  statePlaces: 30,
  description: "Педагогічний університет південної України.",
  alumni: [],
  image: "https://upload.wikimedia.org/wikipedia/commons/9/90/Berdyans%27k_Shmidta_4_Budynok_Cholovichoyi_Gimnaziyi_03_%28YDS_8515%29.JPG",
  lat: 46.7583,
  lon: 36.7866
     },
  {
    name: "Харківський національний університет ім. В. Н. Каразіна",
    faculty: "Журналістика",
    minScore: 174,
    contractScore: 145,
    statePlaces: 40,
    description: "Один з найстаріших університетів України, заснований у 1804 році.",
    alumni: ["Віктор Скакун"],
    image: "https://medicine.karazin.ua/resources/c8548ff.jpg",
    lat: 49.9935,
    lon: 36.2304
  }
  // + Додам ще 95 вузів у наступній частині!
];

let map, markers;

function findUniversities() {
  const input = parseFloat(document.getElementById("scoreInput").value);
  const results = document.getElementById("results");
  results.innerHTML = "";

  if (isNaN(input) || input < 100 || input > 200) {
    results.innerHTML = "<p style='color:red;'>Введіть коректний бал від 100 до 200.</p>";
    document.getElementById("map").style.display = "none";
    return;
  }

  let filtered = universities.filter(u => input >= u.minScore);
  filtered.sort((a, b) => b.minScore - a.minScore); // Сортування

  if (filtered.length === 0) {
    results.innerHTML = "<p>На жаль, з таким балом немає варіантів.</p>";
    document.getElementById("map").style.display = "none";
    return;
  }

  filtered.forEach(u => {
    const div = document.createElement("div");
    div.className = "university";
    div.innerHTML = `
      <img src="${u.image}" alt="Університет">
      <div class="info">
        <h3>${u.name}</h3>
        <p><b>Факультет:</b> ${u.faculty}</p>
        <p><b>Прохідний бал:</b> ${u.minScore}</p>
        <p><b>Контракт:</b> від ${u.contractScore}</p>
        <p><b>Держмісця:</b> ${u.statePlaces}</p>
        <p><b>Опис:</b> ${u.description}</p>
        <p><b>Випускники:</b> ${u.alumni.join(", ")}</p>
      </div>`;
    results.appendChild(div);
  });

  showMap(filtered);
}
function showMap(filtered) {
  document.getElementById("map").style.display = "block";

  if (!map) {
    map = L.map('map').setView([49.0, 32.0], 6); // Центр України
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; OpenStreetMap'
    }).addTo(map);
    markers = [];
  } else {
    markers.forEach(m => m.remove());
    markers = [];
  }

  filtered.forEach(u => {
    const marker = L.marker([u.lat, u.lon]).addTo(map);
    marker.bindPopup(`<b>${u.name}</b><br>${u.faculty}<br>Бал: ${u.minScore}`);
    markers.push(marker);
  });

  if (filtered.length > 0) {
    const first = filtered[0];
    map.setView([first.lat, first.lon], 7);
  }
}
</script>
</body>
</html>

  
