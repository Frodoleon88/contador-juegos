# contador-juegos
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contador de Juegos de Mesa</title>
    <!-- Enlace a Tailwind CSS CDN para estilos -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Configuración de la fuente Inter -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6; /* Fondo gris claro */
        }
        /* Estilos para el modal */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 1000;
        }
        .modal-content {
            background-color: white;
            padding: 2rem;
            border-radius: 0.75rem; /* rounded-lg */
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05); /* shadow-xl */
            width: 90%;
            max-width: 600px;
            max-height: 90vh; /* Para que sea scrollable si el contenido es mucho */
            overflow-y: auto;
            position: relative;
        }
        .modal-close-btn {
            position: absolute;
            top: 1rem;
            right: 1rem;
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: #6b7280; /* gray-500 */
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">
    <div class="bg-white p-8 rounded-lg shadow-xl w-full max-w-2xl">
        <h1 class="text-4xl font-extrabold text-center text-gray-800 mb-8">Colección de Juegos de Mesa</h1>

        <!-- Sección de conteo total -->
        <div class="bg-green-100 p-6 rounded-lg border border-green-300 text-center mb-8 shadow-md">
            <h2 class="text-2xl font-bold text-green-800 mb-2">Total de Juegos en la Colección:</h2>
            <p id="gameCount" class="text-5xl font-extrabold text-green-900">0</p>
        </div>

        <!-- Sección de la lista de juegos -->
        <div class="p-6 bg-gray-50 rounded-lg border border-gray-200 mb-8">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">Mis Juegos</h2>

            <!-- Barra de búsqueda y opciones de ordenación -->
            <div class="flex flex-col sm:flex-row gap-4 mb-4">
                <input type="text" id="searchGamesInput" placeholder="Buscar juego por nombre..." class="flex-grow p-3 border border-gray-300 rounded-md focus:ring-gray-500 focus:border-gray-500 shadow-sm">
                <select id="sortGamesSelect" class="p-3 border border-gray-300 rounded-md focus:ring-gray-500 focus:border-gray-500 shadow-sm bg-white">
                    <option value="name-asc">Ordenar por: Nombre (A-Z)</option>
                    <option value="name-desc">Ordenar por: Nombre (Z-A)</option>
                    <option value="plays-asc">Ordenar por: Partidas Jugadas (Asc)</option>
                    <option value="plays-desc">Ordenar por: Partidas Jugadas (Desc)</option>
                    <option value="bgg-asc">Ordenar por: Nota BGG (Asc)</option>
                    <option value="bgg-desc">Ordenar por: Nota BGG (Desc)</option>
                </select>
            </div>

            <div id="gamesList" class="space-y-4">
                <!-- Los juegos se cargarán aquí -->
                <p id="loadingGames" class="text-gray-500 text-center">Cargando juegos...</p>
            </div>
        </div>

        <!-- Sección para añadir un nuevo juego -->
        <div class="mb-8 p-6 bg-blue-50 rounded-lg border border-blue-200">
            <h2 class="text-2xl font-semibold text-blue-700 mb-4">Añadir Nuevo Juego</h2>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
                <div class="md:col-span-2">
                    <label for="gameName" class="block text-sm font-medium text-gray-700 mb-1">Nombre del Juego:</label>
                    <input type="text" id="gameName" placeholder="Ej: Catán" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 shadow-sm">
                </div>
                <div>
                    <label for="minPlayers" class="block text-sm font-medium text-gray-700 mb-1">Mínimo Jugadores:</label>
                    <input type="number" id="minPlayers" placeholder="Ej: 2" min="1" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 shadow-sm">
                </div>
                <div>
                    <label for="maxPlayers" class="block text-sm font-medium text-gray-700 mb-1">Máximo Jugadores:</label>
                    <input type="number" id="maxPlayers" placeholder="Ej: 4" min="1" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 shadow-sm">
                </div>
                <div>
                    <label for="playingTime" class="block text-sm font-medium text-gray-700 mb-1">Tiempo de Juego (minutos):</label>
                    <input type="number" id="playingTime" placeholder="Ej: 90" min="5" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 shadow-sm">
                </div>
                <div class="md:col-span-2">
                    <label for="bggRating" class="block text-sm font-medium text-gray-700 mb-1">Nota BGG (ingreso manual):</label>
                    <input type="text" id="bggRating" placeholder="Ej: 7.5" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 shadow-sm">
                    <p class="text-xs text-gray-500 mt-1">
                        Debido a restricciones de seguridad, la nota de BGG debe ingresarse manualmente. Puedes buscarla en <a href="https://boardgamegeek.com/" target="_blank" class="text-blue-600 hover:underline">BoardGameGeek.com</a>.
                    </p>
                </div>
            </div>
            <button id="addGameBtn" class="w-full bg-blue-600 text-white py-3 px-6 rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition duration-300 ease-in-out shadow-md hover:shadow-lg">
                Añadir Juego
            </button>
        </div>

        <!-- Botón de Exportar Colección -->
        <div class="mt-8 text-center">
            <button id="exportCollectionBtn" class="bg-gray-600 text-white py-3 px-6 rounded-md hover:bg-gray-700 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition duration-300 ease-in-out shadow-md hover:shadow-lg">
                Exportar Colección (JSON)
            </button>
        </div>

        <!-- Mensaje de carga/error -->
        <div id="messageBox" class="hidden p-4 mb-4 text-sm rounded-lg" role="alert"></div>

        <!-- Mostrar ID de usuario para depuración/colaboración -->
        <div class="mt-8 p-4 bg-gray-100 rounded-lg text-sm text-gray-600 break-words">
            <p>Tu ID de Usuario (para compartir o depurar):</p>
            <p id="userIdDisplay" class="font-mono text-gray-800 mt-1"></p>
        </div>
    </div>

    <!-- Modal para registrar partidas -->
    <div id="gamePlaysModal" class="modal-overlay hidden">
        <div class="modal-content">
            <button class="modal-close-btn" id="closeModalBtn">&times;</button>
            <h2 id="modalGameTitle" class="text-3xl font-bold text-gray-800 mb-6 text-center">Registrar Partida de [Nombre del Juego]</h2>

            <!-- Estadísticas de Partidas -->
            <div class="mb-6 p-4 bg-yellow-50 rounded-lg border border-yellow-200">
                <h3 class="text-xl font-semibold text-yellow-700 mb-3">Estadísticas de Partidas</h3>
                <p class="text-sm text-gray-700">Primera Partida: <span id="firstPlayDate" class="font-bold">N/A</span></p>
                <p class="text-sm text-gray-700">Última Partida: <span id="lastPlayDate" class="font-bold">N/A</span></p>
                <p class="text-sm text-gray-700">Ganador Más Frecuente: <span id="mostFrequentWinner" class="font-bold">N/A</span></p>
            </div>

            <!-- Formulario para añadir partida -->
            <div class="mb-6 p-4 bg-purple-50 rounded-lg border border-purple-200">
                <h3 class="text-xl font-semibold text-purple-700 mb-3">Añadir Nueva Partida</h3>
                <div class="grid grid-cols-1 gap-4 mb-4">
                    <div>
                        <label for="playDate" class="block text-sm font-medium text-gray-700 mb-1">Fecha:</label>
                        <input type="date" id="playDate" class="w-full p-3 border border-gray-300 rounded-md focus:ring-purple-500 focus:border-purple-500 shadow-sm">
                    </div>
                    <div>
                        <label for="playPlayers" class="block text-sm font-medium text-gray-700 mb-1">Jugadores (separados por comas):</label>
                        <input type="text" id="playPlayers" placeholder="Ej: Juan, Ana, Pedro" class="w-full p-3 border border-gray-300 rounded-md focus:ring-purple-500 focus:border-purple-500 shadow-sm">
                    </div>
                    <div>
                        <label for="playWinners" class="block text-sm font-medium text-gray-700 mb-1">Ganadores (separados por comas):</label>
                        <input type="text" id="playWinners" placeholder="Ej: Juan" class="w-full p-3 border border-gray-300 rounded-md focus:ring-purple-500 focus:border-purple-500 shadow-sm">
                    </div>
                    <div>
                        <label for="playPhotoUrl" class="block text-sm font-medium text-gray-700 mb-1">URL de la Foto:</label>
                        <input type="url" id="playPhotoUrl" placeholder="https://ejemplo.com/tu-foto.jpg" class="w-full p-3 border border-gray-300 rounded-md focus:ring-purple-500 focus:border-purple-500 shadow-sm">
                        <p class="text-xs text-gray-500 mt-1">
                            Sube tu foto a un servicio externo (ej: Imgur) y pega aquí la URL directa de la imagen.
                        </p>
                    </div>
                </div>
                <button id="addPlayBtn" class="w-full bg-purple-600 text-white py-3 px-6 rounded-md hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:ring-offset-2 transition duration-300 ease-in-out shadow-md hover:shadow-lg">
                    Registrar Partida
                </button>
            </div>

            <!-- Lista de partidas jugadas -->
            <h3 class="text-xl font-semibold text-gray-700 mb-3">Historial de Partidas</h3>
            <div id="gamePlaysList" class="space-y-3">
                <p id="loadingPlays" class="text-gray-500 text-center">Cargando historial de partidas...</p>
                <!-- Las partidas se cargarán aquí -->
            </div>
        </div>
    </div>

    <!-- Modal para editar juego -->
    <div id="editGameModal" class="modal-overlay hidden">
        <div class="modal-content">
            <button class="modal-close-btn" id="closeEditModalBtn">&times;</button>
            <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Editar Juego</h2>

            <div class="mb-6 p-4 bg-yellow-50 rounded-lg border border-yellow-200">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
                    <div class="md:col-span-2">
                        <label for="editGameName" class="block text-sm font-medium text-gray-700 mb-1">Nombre del Juego:</label>
                        <input type="text" id="editGameName" class="w-full p-3 border border-gray-300 rounded-md focus:ring-yellow-500 focus:border-yellow-500 shadow-sm">
                    </div>
                    <div>
                        <label for="editMinPlayers" class="block text-sm font-medium text-gray-700 mb-1">Mínimo Jugadores:</label>
                        <input type="number" id="editMinPlayers" min="1" class="w-full p-3 border border-gray-300 rounded-md focus:ring-yellow-500 focus:border-yellow-500 shadow-sm">
                    </div>
                    <div>
                        <label for="editMaxPlayers" class="block text-sm font-medium text-gray-700 mb-1">Máximo Jugadores:</label>
                        <input type="number" id="editMaxPlayers" min="1" class="w-full p-3 border border-gray-300 rounded-md focus:ring-yellow-500 focus:border-yellow-500 shadow-sm">
                    </div>
                    <div>
                        <label for="editPlayingTime" class="block text-sm font-medium text-gray-700 mb-1">Tiempo de Juego (minutos):</label>
                        <input type="number" id="editPlayingTime" min="5" class="w-full p-3 border border-gray-300 rounded-md focus:ring-yellow-500 focus:border-yellow-500 shadow-sm">
                    </div>
                    <div class="md:col-span-2">
                        <label for="editBggRating" class="block text-sm font-medium text-gray-700 mb-1">Nota BGG (ingreso manual):</label>
                        <input type="text" id="editBggRating" class="w-full p-3 border border-gray-300 rounded-md focus:ring-yellow-500 focus:border-yellow-500 shadow-sm">
                    </div>
                </div>
                <button id="saveEditGameBtn" class="w-full bg-yellow-600 text-white py-3 px-6 rounded-md hover:bg-yellow-700 focus:outline-none focus:ring-2 focus:ring-yellow-500 focus:ring-offset-2 transition duration-300 ease-in-out shadow-md hover:shadow-lg">
                    Guardar Cambios
                </button>
            </div>
        </div>
    </div>

    <!-- Script de Firebase -->
    <script type="module">
        // Importaciones de Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, doc, deleteDoc, updateDoc, increment, getDocs } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Variables globales para Firebase y la aplicación
        let app;
        let db;
        let auth;
        let userId = 'anonimo'; // Valor predeterminado antes de la autenticación
        let isAuthReady = false; // Bandera para asegurar que la autenticación está lista
        let currentOpenGameId = null; // Para saber qué juego está abierto en el modal de partidas
        let currentEditGameId = null; // Para saber qué juego está abierto en el modal de edición
        let allGamesData = []; // Almacena todos los juegos para filtrado y ordenación local

        // Obtener las variables de configuración de Firebase del entorno de Canvas
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // Referencias a elementos del DOM
        const gameNameInput = document.getElementById('gameName');
        const minPlayersInput = document.getElementById('minPlayers');
        const maxPlayersInput = document.getElementById('maxPlayers');
        const playingTimeInput = document.getElementById('playingTime');
        const bggRatingInput = document.getElementById('bggRating');
        const addGameBtn = document.getElementById('addGameBtn');
        const gamesListDiv = document.getElementById('gamesList');
        const gameCountDisplay = document.getElementById('gameCount');
        const loadingGamesText = document.getElementById('loadingGames');
        const userIdDisplay = document.getElementById('userIdDisplay');
        const messageBox = document.getElementById('messageBox');
        const searchGamesInput = document.getElementById('searchGamesInput');
        const sortGamesSelect = document.getElementById('sortGamesSelect');
        const exportCollectionBtn = document.getElementById('exportCollectionBtn');

        // Referencias a elementos del modal de partidas
        const gamePlaysModal = document.getElementById('gamePlaysModal');
        const closeModalBtn = document.getElementById('closeModalBtn');
        const modalGameTitle = document.getElementById('modalGameTitle');
        const playDateInput = document.getElementById('playDate');
        const playPlayersInput = document.getElementById('playPlayers');
        const playWinnersInput = document.getElementById('playWinners');
        const playPhotoUrlInput = document.getElementById('playPhotoUrl');
        const addPlayBtn = document.getElementById('addPlayBtn');
        const gamePlaysListDiv = document.getElementById('gamePlaysList');
        const loadingPlaysText = document.getElementById('loadingPlays');
        const firstPlayDateSpan = document.getElementById('firstPlayDate');
        const lastPlayDateSpan = document.getElementById('lastPlayDate');
        const mostFrequentWinnerSpan = document.getElementById('mostFrequentWinner');

        // Referencias a elementos del modal de edición
        const editGameModal = document.getElementById('editGameModal');
        const closeEditModalBtn = document.getElementById('closeEditModalBtn');
        const editGameNameInput = document.getElementById('editGameName');
        const editMinPlayersInput = document.getElementById('editMinPlayers');
        const editMaxPlayersInput = document.getElementById('editMaxPlayers');
        const editPlayingTimeInput = document.getElementById('editPlayingTime');
        const editBggRatingInput = document.getElementById('editBggRating');
        const saveEditGameBtn = document.getElementById('saveEditGameBtn');


        /**
         * Muestra un mensaje en la interfaz de usuario.
         * @param {string} message El mensaje a mostrar.
         * @param {string} type El tipo de mensaje ('success', 'error', 'info').
         */
        function showMessage(message, type) {
            messageBox.textContent = message;
            messageBox.className = `p-4 mb-4 text-sm rounded-lg ${type === 'success' ? 'bg-green-100 text-green-800' : type === 'error' ? 'bg-red-100 text-red-800' : 'bg-blue-100 text-blue-800'}`;
            messageBox.classList.remove('hidden');
            setTimeout(() => {
                messageBox.classList.add('hidden');
            }, 3000); // Oculta el mensaje después de 3 segundos
        }

        /**
         * Inicializa Firebase y configura la autenticación.
         */
        async function initializeFirebase() {
            try {
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Listener para el estado de autenticación
                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        userId = user.uid;
                        userIdDisplay.textContent = userId;
                        isAuthReady = true;
                        console.log("Usuario autenticado:", userId);
                        // Una vez autenticado, carga los juegos
                        loadGames();
                    } else {
                        // Si no hay usuario, intenta iniciar sesión anónimamente o con el token proporcionado
                        try {
                            if (initialAuthToken) {
                                await signInWithCustomToken(auth, initialAuthToken);
                            } else {
                                await signInAnonymously(auth);
                            }
                        } catch (error) {
                            console.error("Error al iniciar sesión anónimamente o con token:", error);
                            showMessage("Error al iniciar sesión. Algunas funciones pueden no estar disponibles.", "error");
                            userId = 'anonimo_no_auth'; // En caso de fallo de autenticación
                            userIdDisplay.textContent = userId;
                            isAuthReady = true; // Permite que la app intente cargar datos incluso sin auth completa
                            loadGames(); // Intenta cargar juegos incluso sin autenticación completa (pueden fallar si las reglas de seguridad lo impiden)
                        }
                    }
                });
            } catch (error) {
                console.error("Error al inicializar Firebase:", error);
                showMessage("Error grave al inicializar la aplicación. Intenta recargar.", "error");
            }
        }

        /**
         * Añade un nuevo juego a Firestore.
         */
        async function addGame() {
            if (!isAuthReady) {
                showMessage("La aplicación se está inicializando. Por favor, espera un momento.", "info");
                return;
            }

            const name = gameNameInput.value.trim();
            const minPlayers = parseInt(minPlayersInput.value);
            const maxPlayers = parseInt(maxPlayersInput.value);
            const playingTime = parseInt(playingTimeInput.value);
            const bggRating = bggRatingInput.value.trim();

            if (!name || isNaN(minPlayers) || isNaN(maxPlayers) || isNaN(playingTime)) {
                showMessage("Por favor, rellena todos los campos correctamente.", "error");
                return;
            }

            if (minPlayers > maxPlayers) {
                showMessage("El número mínimo de jugadores no puede ser mayor que el máximo.", "error");
                return;
            }

            try {
                // Ruta de la colección para datos públicos (compartidos)
                const gamesCollectionRef = collection(db, `artifacts/${appId}/public/data/boardGames`);
                await addDoc(gamesCollectionRef, {
                    name: name,
                    minPlayers: minPlayers,
                    maxPlayers: maxPlayers,
                    playingTime: playingTime,
                    bggRating: bggRating,
                    playsCount: 0, // Initialize plays count
                    createdAt: new Date(),
                    userId: userId // Registrar quién añadió el juego
                });
                showMessage("Juego añadido con éxito!", "success");
                // Limpiar los campos del formulario
                gameNameInput.value = '';
                minPlayersInput.value = '';
                maxPlayersInput.value = '';
                playingTimeInput.value = '';
                bggRatingInput.value = '';
            } catch (e) {
                console.error("Error al añadir el documento: ", e);
                showMessage("Error al añadir el juego. Intenta de nuevo.", "error");
            }
        }

        /**
         * Carga los juegos desde Firestore y los almacena en allGamesData.
         * Luego llama a renderGamesList para mostrar los juegos.
         */
        function loadGames() {
            if (!db || !isAuthReady) {
                console.log("Firestore no está inicializado o la autenticación no está lista.");
                return;
            }

            const gamesCollectionRef = collection(db, `artifacts/${appId}/public/data/boardGames`);
            const q = query(gamesCollectionRef);

            onSnapshot(q, (snapshot) => {
                allGamesData = []; // Reset allGamesData
                snapshot.forEach((gameDoc) => {
                    allGamesData.push({ id: gameDoc.id, ...gameDoc.data() });
                });
                renderGamesList(); // Render with current search/sort filters
            }, (error) => {
                console.error("Error al obtener documentos: ", error);
                loadingGamesText.textContent = 'Error al cargar los juegos.';
                loadingGamesText.classList.remove('hidden');
                showMessage("Error al cargar los juegos. Intenta recargar la página.", "error");
            });
        }

        /**
         * Renderiza la lista de juegos en el DOM, aplicando búsqueda y ordenación.
         */
        function renderGamesList() {
            gamesListDiv.innerHTML = ''; // Limpiar la lista actual
            loadingGamesText.classList.add('hidden'); // Ocultar mensaje de carga

            let filteredGames = [...allGamesData];

            // Aplicar búsqueda
            const searchTerm = searchGamesInput.value.toLowerCase();
            if (searchTerm) {
                filteredGames = filteredGames.filter(game =>
                    game.name.toLowerCase().includes(searchTerm)
                );
            }

            // Aplicar ordenación
            const sortOption = sortGamesSelect.value;
            filteredGames.sort((a, b) => {
                switch (sortOption) {
                    case 'name-asc':
                        return a.name.localeCompare(b.name);
                    case 'name-desc':
                        return b.name.localeCompare(a.name);
                    case 'plays-asc':
                        return (a.playsCount || 0) - (b.playsCount || 0);
                    case 'plays-desc':
                        return (b.playsCount || 0) - (a.playsCount || 0);
                    case 'bgg-asc':
                        return parseFloat(a.bggRating || 0) - parseFloat(b.bggRating || 0);
                    case 'bgg-desc':
                        return parseFloat(b.bggRating || 0) - parseFloat(a.bggRating || 0);
                    default:
                        return 0;
                }
            });

            gameCountDisplay.textContent = filteredGames.length; // Actualizar el conteo total de juegos visibles

            if (filteredGames.length === 0) {
                gamesListDiv.innerHTML = '<p class="text-gray-500 text-center">No se encontraron juegos que coincidan con tu búsqueda o no hay juegos en tu colección aún. ¡Añade uno!</p>';
            } else {
                filteredGames.forEach((game) => {
                    const gameItem = document.createElement('div');
                    gameItem.id = `game-${game.id}`;
                    gameItem.className = 'bg-white p-4 rounded-md shadow-sm border border-gray-200 flex flex-col sm:flex-row justify-between items-start sm:items-center';
                    gameItem.innerHTML = `
                        <div class="mb-2 sm:mb-0 flex-grow">
                            <p class="text-lg font-semibold text-gray-800">${game.name}</p>
                            <p class="text-sm text-gray-600">
                                Jugadores: ${game.minPlayers}-${game.maxPlayers} | Tiempo: ${game.playingTime} min
                            </p>
                            <p class="text-sm text-gray-600">
                                Nota BGG: <span class="font-bold">${game.bggRating || 'N/A'}</span> | Partidas Jugadas: <span class="font-bold">${game.playsCount || 0}</span>
                            </p>
                            <p class="text-xs text-gray-400">Añadido por: ${game.userId.substring(0, 8)}...</p>
                        </div>
                        <div class="flex flex-col sm:flex-row gap-2">
                            <button data-id="${game.id}" data-name="${game.name}" class="view-plays-btn bg-purple-500 text-white px-4 py-2 rounded-md hover:bg-purple-600 transition duration-200 ease-in-out shadow-sm">
                                Ver Partidas
                            </button>
                            <button data-id="${game.id}" class="edit-game-btn bg-yellow-500 text-white px-4 py-2 rounded-md hover:bg-yellow-600 transition duration-200 ease-in-out shadow-sm">
                                Editar
                            </button>
                            <button data-id="${game.id}" class="delete-btn bg-red-500 text-white px-4 py-2 rounded-md hover:bg-red-600 transition duration-200 ease-in-out shadow-sm">
                                Eliminar
                            </button>
                        </div>
                    `;
                    gamesListDiv.appendChild(gameItem);
                });
            }
        }

        /**
         * Elimina un juego de Firestore.
         * @param {string} gameId El ID del documento del juego a eliminar.
         */
        async function deleteGame(gameId) {
            if (!isAuthReady) {
                showMessage("La aplicación se está inicializando. Por favor, espera un momento.", "info");
                return;
            }
            try {
                // Ruta del documento a eliminar
                const gameDocRef = doc(db, `artifacts/${appId}/public/data/boardGames`, gameId);
                await deleteDoc(gameDocRef);
                showMessage("Juego eliminado con éxito!", "success");
            } catch (e) {
                console.error("Error al eliminar el documento: ", e);
                showMessage("Error al eliminar el juego. Intenta de nuevo.", "error");
            }
        }

        /**
         * Abre el modal para registrar y ver partidas de un juego específico.
         * @param {string} gameId El ID del juego.
         * @param {string} gameName El nombre del juego.
         */
        function openGamePlaysModal(gameId, gameName) {
            currentOpenGameId = gameId;
            modalGameTitle.textContent = `Registrar Partida de ${gameName}`;
            gamePlaysModal.classList.remove('hidden');
            loadGamePlays(gameId); // Cargar las partidas para este juego
        }

        /**
         * Cierra el modal de partidas.
         */
        function closeGamePlaysModal() {
            gamePlaysModal.classList.add('hidden');
            gamePlaysListDiv.innerHTML = ''; // Limpiar la lista al cerrar
            playDateInput.value = '';
            playPlayersInput.value = '';
            playWinnersInput.value = '';
            playPhotoUrlInput.value = '';
            currentOpenGameId = null;
            // Reset stats
            firstPlayDateSpan.textContent = 'N/A';
            lastPlayDateSpan.textContent = 'N/A';
            mostFrequentWinnerSpan.textContent = 'N/A';
        }

        /**
         * Carga las partidas jugadas para un juego específico desde Firestore.
         * Calcula y muestra estadísticas.
         * @param {string} gameId El ID del juego.
         */
        function loadGamePlays(gameId) {
            if (!db || !isAuthReady) {
                console.log("Firestore no está inicializado o la autenticación no está lista.");
                return;
            }

            const gamePlaysCollectionRef = collection(db, `artifacts/${appId}/public/data/boardGames/${gameId}/gamePlays`);
            const q = query(gamePlaysCollectionRef);

            onSnapshot(q, (snapshot) => {
                gamePlaysListDiv.innerHTML = '';
                loadingPlaysText.classList.add('hidden');
                let allPlays = [];

                if (snapshot.empty) {
                    gamePlaysListDiv.innerHTML = '<p class="text-gray-500 text-center">No hay partidas registradas para este juego aún.</p>';
                    // Reset stats if no plays
                    firstPlayDateSpan.textContent = 'N/A';
                    lastPlayDateSpan.textContent = 'N/A';
                    mostFrequentWinnerSpan.textContent = 'N/A';
                } else {
                    snapshot.forEach((playDoc) => {
                        const play = playDoc.data();
                        allPlays.push(play); // Add to array for stats calculation

                        const playItem = document.createElement('div');
                        playItem.className = 'bg-white p-3 rounded-md shadow-sm border border-gray-200 flex flex-col sm:flex-row justify-between items-start sm:items-center';

                        const formattedDate = play.date ? new Date(play.date + 'T00:00:00').toLocaleDateString('es-ES') : 'N/A'; // Add T00:00:00 for correct date parsing
                        const playersText = play.players && play.players.length > 0 ? play.players.join(', ') : 'N/A';
                        const winnersText = play.winners && play.winners.length > 0 ? play.winners.join(', ') : 'N/A';

                        playItem.innerHTML = `
                            <div class="mb-2 sm:mb-0 flex-grow">
                                <p class="text-md font-semibold text-gray-800">Fecha: ${formattedDate}</p>
                                <p class="text-sm text-gray-600">Jugadores: ${playersText}</p>
                                <p class="text-sm text-gray-600">Ganadores: ${winnersText}</p>
                                ${play.photoUrl ? `<img src="${play.photoUrl}" onerror="this.onerror=null;this.src='https://placehold.co/100x75/E0E0E0/666666?text=No+Image';" alt="Foto de la partida" class="mt-2 rounded-md max-w-[100px] max-h-[75px] object-cover">` : ''}
                                <p class="text-xs text-gray-400 mt-1">Registrado por: ${play.playedByUserId ? play.playedByUserId.substring(0, 8) + '...' : 'N/A'}</p>
                            </div>
                            <button data-play-id="${playDoc.id}" class="delete-play-btn bg-red-500 text-white px-3 py-1 rounded-md hover:bg-red-600 transition duration-200 ease-in-out shadow-sm text-sm">
                                Eliminar Partida
                            </button>
                        `;
                        gamePlaysListDiv.appendChild(playItem);
                    });

                    // Calculate and display stats
                    if (allPlays.length > 0) {
                        const dates = allPlays.map(play => new Date(play.date + 'T00:00:00'));
                        const firstDate = new Date(Math.min(...dates));
                        const lastDate = new Date(Math.max(...dates));

                        firstPlayDateSpan.textContent = firstDate.toLocaleDateString('es-ES');
                        lastPlayDateSpan.textContent = lastDate.toLocaleDateString('es-ES');

                        const winnerCounts = {};
                        allPlays.forEach(play => {
                            if (play.winners && play.winners.length > 0) {
                                play.winners.forEach(winner => {
                                    const trimmedWinner = winner.trim();
                                    winnerCounts[trimmedWinner] = (winnerCounts[trimmedWinner] || 0) + 1;
                                });
                            }
                        });

                        let mostFrequentWinner = 'N/A';
                        let maxWins = 0;
                        for (const winner in winnerCounts) {
                            if (winnerCounts[winner] > maxWins) {
                                maxWins = winnerCounts[winner];
                                mostFrequentWinner = winner;
                            } else if (winnerCounts[winner] === maxWins && mostFrequentWinner !== 'N/A') {
                                // Handle ties for most frequent winner
                                mostFrequentWinner += `, ${winner}`;
                            }
                        }
                        mostFrequentWinnerSpan.textContent = mostFrequentWinner !== 'N/A' ? `${mostFrequentWinner} (${maxWins} victorias)` : 'N/A';

                    } else {
                        firstPlayDateSpan.textContent = 'N/A';
                        lastPlayDateSpan.textContent = 'N/A';
                        mostFrequentWinnerSpan.textContent = 'N/A';
                    }
                }
            }, (error) => {
                console.error("Error al cargar partidas: ", error);
                loadingPlaysText.textContent = 'Error al cargar el historial de partidas.';
                loadingPlaysText.classList.remove('hidden');
                showMessage("Error al cargar el historial de partidas.", "error");
            });
        }

        /**
         * Añade una nueva partida jugada a un juego.
         */
        async function addGamePlay() {
            if (!isAuthReady || !currentOpenGameId) {
                showMessage("Error: No se ha seleccionado un juego o la aplicación no está lista.", "error");
                return;
            }

            const date = playDateInput.value;
            const players = playPlayersInput.value.split(',').map(p => p.trim()).filter(p => p !== '');
            const winners = playWinnersInput.value.split(',').map(w => w.trim()).filter(w => w !== '');
            const photoUrl = playPhotoUrlInput.value.trim();

            if (!date || players.length === 0) {
                showMessage("Por favor, introduce al menos la fecha y los jugadores.", "error");
                return;
            }

            try {
                // Añadir la partida a la subcolección
                const gamePlaysCollectionRef = collection(db, `artifacts/${appId}/public/data/boardGames/${currentOpenGameId}/gamePlays`);
                await addDoc(gamePlaysCollectionRef, {
                    date: date,
                    players: players,
                    winners: winners,
                    photoUrl: photoUrl,
                    playedByUserId: userId,
                    createdAt: new Date()
                });

                // Incrementar el contador de partidas en el documento del juego principal
                const gameDocRef = doc(db, `artifacts/${appId}/public/data/boardGames`, currentOpenGameId);
                await updateDoc(gameDocRef, {
                    playsCount: increment(1)
                });

                showMessage("Partida registrada con éxito!", "success");
                // Limpiar los campos del formulario de partida
                playDateInput.value = '';
                playPlayersInput.value = '';
                playWinnersInput.value = '';
                playPhotoUrlInput.value = '';
            } catch (e) {
                console.error("Error al registrar la partida: ", e);
                showMessage("Error al registrar la partida. Intenta de nuevo.", "error");
            }
        }

        /**
         * Elimina una partida jugada específica.
         * @param {string} gameId El ID del juego padre.
         * @param {string} playId El ID de la partida a eliminar.
         */
        async function deleteGamePlay(gameId, playId) {
            if (!isAuthReady) {
                showMessage("La aplicación se está inicializando. Por favor, espera un momento.", "info");
                return;
            }
            try {
                const playDocRef = doc(db, `artifacts/${appId}/public/data/boardGames/${gameId}/gamePlays`, playId);
                await deleteDoc(playDocRef);

                // Decrementar el contador de partidas en el documento del juego principal
                const gameDocRef = doc(db, `artifacts/${appId}/public/data/boardGames`, gameId);
                await updateDoc(gameDocRef, {
                    playsCount: increment(-1)
                });

                showMessage("Partida eliminada con éxito!", "success");
            } catch (e) {
                console.error("Error al eliminar la partida: ", e);
                showMessage("Error al eliminar la partida. Intenta de nuevo.", "error");
            }
        }

        /**
         * Abre el modal de edición de juego y precarga los datos.
         * @param {string} gameId El ID del juego a editar.
         */
        function openEditGameModal(gameId) {
            currentEditGameId = gameId;
            const gameToEdit = allGamesData.find(game => game.id === gameId);

            if (gameToEdit) {
                editGameNameInput.value = gameToEdit.name;
                editMinPlayersInput.value = gameToEdit.minPlayers;
                editMaxPlayersInput.value = gameToEdit.maxPlayers;
                editPlayingTimeInput.value = gameToEdit.playingTime;
                editBggRatingInput.value = gameToEdit.bggRating;
                editGameModal.classList.remove('hidden');
            } else {
                showMessage("No se encontró el juego para editar.", "error");
            }
        }

        /**
         * Cierra el modal de edición de juego.
         */
        function closeEditModal() {
            editGameModal.classList.add('hidden');
            currentEditGameId = null;
        }

        /**
         * Guarda los cambios de un juego editado en Firestore.
         */
        async function saveEditedGame() {
            if (!isAuthReady || !currentEditGameId) {
                showMessage("Error: No se ha seleccionado un juego para editar o la aplicación no está lista.", "error");
                return;
            }

            const name = editGameNameInput.value.trim();
            const minPlayers = parseInt(editMinPlayersInput.value);
            const maxPlayers = parseInt(editMaxPlayersInput.value);
            const playingTime = parseInt(editPlayingTimeInput.value);
            const bggRating = editBggRatingInput.value.trim();

            if (!name || isNaN(minPlayers) || isNaN(maxPlayers) || isNaN(playingTime)) {
                showMessage("Por favor, rellena todos los campos correctamente para editar.", "error");
                return;
            }

            if (minPlayers > maxPlayers) {
                showMessage("El número mínimo de jugadores no puede ser mayor que el máximo.", "error");
                return;
            }

            try {
                const gameDocRef = doc(db, `artifacts/${appId}/public/data/boardGames`, currentEditGameId);
                await updateDoc(gameDocRef, {
                    name: name,
                    minPlayers: minPlayers,
                    maxPlayers: maxPlayers,
                    playingTime: playingTime,
                    bggRating: bggRating
                });
                showMessage("Juego actualizado con éxito!", "success");
                closeEditModal();
            } catch (e) {
                console.error("Error al actualizar el juego: ", e);
                showMessage("Error al actualizar el juego. Intenta de nuevo.", "error");
            }
        }

        /**
         * Exporta toda la colección de juegos y sus partidas a un archivo JSON.
         */
        async function exportCollection() {
            if (!isAuthReady) {
                showMessage("La aplicación se está inicializando. Por favor, espera un momento.", "info");
                return;
            }

            showMessage("Exportando colección...", "info");
            try {
                const gamesCollectionRef = collection(db, `artifacts/${appId}/public/data/boardGames`);
                const gamesSnapshot = await getDocs(gamesCollectionRef);
                const exportedData = [];

                for (const gameDoc of gamesSnapshot.docs) {
                    const gameData = { id: gameDoc.id, ...gameDoc.data() };
                    const playsCollectionRef = collection(db, `artifacts/${appId}/public/data/boardGames/${gameDoc.id}/gamePlays`);
                    const playsSnapshot = await getDocs(playsCollectionRef);
                    gameData.gamePlays = playsSnapshot.docs.map(playDoc => ({ id: playDoc.id, ...playDoc.data() }));
                    exportedData.push(gameData);
                }

                const jsonString = JSON.stringify(exportedData, null, 2);
                const blob = new Blob([jsonString], { type: 'application/json' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `board_game_collection_${new Date().toISOString().slice(0,10)}.json`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(url);
                showMessage("Colección exportada con éxito!", "success");

            } catch (e) {
                console.error("Error al exportar la colección: ", e);
                showMessage("Error al exportar la colección. Intenta de nuevo.", "error");
            }
        }


        // Event Listeners
        addGameBtn.addEventListener('click', addGame);
        closeModalBtn.addEventListener('click', closeGamePlaysModal);
        addPlayBtn.addEventListener('click', addGamePlay);
        closeEditModalBtn.addEventListener('click', closeEditModal);
        saveEditGameBtn.addEventListener('click', saveEditedGame);
        searchGamesInput.addEventListener('keyup', renderGamesList); // Re-render on search input
        sortGamesSelect.addEventListener('change', renderGamesList); // Re-render on sort change
        exportCollectionBtn.addEventListener('click', exportCollection);

        // Delegación de eventos para los botones de eliminar juego, ver partidas y editar juego
        gamesListDiv.addEventListener('click', (event) => {
            if (event.target.classList.contains('delete-btn')) {
                const gameId = event.target.dataset.id;
                if (gameId) {
                    deleteGame(gameId);
                }
            } else if (event.target.classList.contains('view-plays-btn')) {
                const gameId = event.target.dataset.id;
                const gameName = event.target.dataset.name;
                if (gameId && gameName) {
                    openGamePlaysModal(gameId, gameName);
                }
            } else if (event.target.classList.contains('edit-game-btn')) {
                const gameId = event.target.dataset.id;
                if (gameId) {
                    openEditGameModal(gameId);
                }
            }
        });

        // Delegación de eventos para los botones de eliminar partida dentro del modal
        gamePlaysListDiv.addEventListener('click', (event) => {
            if (event.target.classList.contains('delete-play-btn')) {
                const playId = event.target.dataset.playId;
                if (playId && currentOpenGameId) {
                    deleteGamePlay(currentOpenGameId, playId);
                }
            }
        });

        // Inicializar Firebase cuando la ventana se haya cargado
        window.onload = initializeFirebase;
    </script>
</body>
</html>
