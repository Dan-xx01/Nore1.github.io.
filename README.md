# Nore1.github.io.
войс чат бейб
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Voice Chat</title>
    <style>
        body {
            background-color: #0a0a0a; /* Черный фон */
            color: white;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }

        .container {
            text-align: center;
            background: #1a1a1a;
            padding: 40px;
            border-radius: 20px;
            box-shadow: 0 0 30px rgba(138, 43, 226, 0.3); /* Фиолетовое свечение */
            border: 1px solid #4b0082;
        }

        h1 { color: #a020f0; }

        input {
            padding: 10px;
            border-radius: 5px;
            border: none;
            margin: 10px;
            width: 250px;
        }

        button {
            padding: 10px 20px;
            background-color: #a020f0;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: 0.3s;
        }

        button:hover { background-color: #8a2be2; }

        #status { margin-top: 20px; color: #ccc; }

        .peer-info {
            margin: 20px 0;
            font-size: 1.2em;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Voice Chat</h1>
    <p>Твой ID (для друга): <span id="my-id">...</span></p>
    
    <div>
        <input type="text" id="remote-id" placeholder="Введите ID друга">
        <button id="connect-btn">Подключиться</button>
    </div>

    <div id="status">Нажми "Подключиться", чтобы начать...</div>
</div>

<!-- Подключаем библиотеку PeerJS -->
<script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>

<script>
    const myIdDisplay = document.getElementById('my-id');
    const remoteIdInput = document.getElementById('remote-id');
    const connectBtn = document.getElementById('connect-btn');
    const statusDisplay = document.getElementById('status');

    // Создаем объект Peer (он автоматически получит уникальный ID)
    const peer = new Peer(); 

    let localStream;

    // Когда Peer готов, показываем свой ID
    peer.on('open', (id) => {
        myIdDisplay.innerText = id;
        statusDisplay.innerText = "Готов к подключению!";
    });

    // Запрашиваем доступ к микрофону при загрузке страницы
    navigator.mediaDevices.getUserMedia({ audio: true, video: false })
        .then(stream => {
            localStream = stream;
            console.log("Микрофон включен");
        })
        .catch(err => {
            statusDisplay.innerText = "Ошибка микрофона: " + err;
        });

    // Когда кто-то подключается к нам
    peer.on('call', (call) => {
        call.answer(localStream); // Отвечаем своим потоком звука
        call.on('stream', (remoteStream) => {
            statusDisplay.innerText = "Собеседник подключен!";
            playRemoteStream(remoteStream);
        });
    });

    // Функция для подключения к другу по его ID
    connectBtn.onclick = () => {
        const remoteId = remoteIdInput.value;
        if (remoteId) {
            statusDisplay.innerText = "Подключение к " + remoteId + "...";
            const call = peer.call(remoteId, localStream);
            call.on('stream', (remoteStream) => {
                statusDisplay.innerText = "Собеседник подключен!";
                playRemoteStream(remoteStream);
            });
        }
    };

    // Функция для воспроизведения звука друга
    function playRemoteStream(stream) {
        const audio = document.createElement('audio');
        audio.srcObject = stream;
        audio.autoplay = true;
        document.body.appendChild(audio);
    }
</script>

</body>
</html>
