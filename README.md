<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Rastreo en Tiempo Real</title>
    <script src="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.js"></script>
    <link href="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.css" rel="stylesheet" />
    <style>
        body { margin: 0; padding: 0; }
        #map { width: 100%; height: 100vh; }
    </style>
</head>
<body>
    <div id="map"></div>

    <script type="module">
        // Importar Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.5.0/firebase-app.js";
        import { getDatabase, ref, onValue } from "https://www.gstatic.com/firebasejs/11.5.0/firebase-database.js";

        // Configuración de Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyCExmK7ERizWFcEJZdBLyjwQ4tT8b9zSGQ",
            authDomain: "conremb.firebaseapp.com",
            databaseURL: "https://conremb-default-rtdb.firebaseio.com",
            projectId: "conremb",
            storageBucket: "conremb.firebasestorage.app",
            messagingSenderId: "804316261663",
            appId: "1:804316261663:web:3bd7a0e4ad8c7b124f542a",
            measurementId: "G-ZWRYP3JLQ1"
        };

        // Inicializar Firebase
        const app = initializeApp(firebaseConfig);
        const database = getDatabase(app);

        // Configuración de Mapbox
        const mapboxToken = 'pk.eyJ1IjoiYnlyb245OTk5IiwiYSI6ImNtOHE1YWh5eDBocDIya3BuemkxenB5bHAifQ.u0VgibitEx2mbPoa6o-ZWQ';
        mapboxgl.accessToken = mapboxToken;

        // Inicializar mapa
        const map = new mapboxgl.Map({
            container: 'map',
            style: 'mapbox://styles/mapbox/streets-v11',
            center: [-68.119938, -16.5034131], // Coordenadas iniciales
            zoom: 15
        });

        // Crear marcador
        const marker = new mapboxgl.Marker()
            .setLngLat([-68.119938, -16.5034131])
            .addTo(map);

        // Referencia a la ubicación en Firebase
        const ubicacionRef = ref(database, 'conremb/current_location');

        // Escuchar cambios en la base de datos en tiempo real
        onValue(ubicacionRef, (snapshot) => {
            const data = snapshot.val();
            console.log("Datos recibidos:", data);  
            
            if (data && typeof data.latitude === 'number' && typeof data.longitude === 'number') {
                const { latitude, longitude } = data;

                // Actualizar la posición del marcador y el centro del mapa
                marker.setLngLat([longitude, latitude]);
                map.setCenter([longitude, latitude]);
            } else {
                console.log("Datos inválidos recibidos desde Firebase.");
            }
        });
    </script>
</body>
</html>
