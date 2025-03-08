# Ejemplo con Websockets

## El repositorio presenta dos partes:

- backend : instalación básica de express
- index.html : fichero para gestionar el frontend de nuestros websockets

## Instalación

Acceder a la carpeta `backend` y ejecutar:

```
npm install
```

# Ejemplo de websocket en servidor

```
// Creates new WebSocket object with a wss URI as the parameter
const socket = new WebSocket('wss://game.example.com/ws/updates');

// Fired when a connection with a WebSocket is opened
socket.onopen = function () {
  setInterval(function() {
    if (socket.bufferedAmount == 0)
      socket.send(getUpdateData());
  }, 50);
};

// Fired when data is received through a WebSocket
socket.onmessage = function(event) {
  handleUpdateData(event.data);
};

// Fired when a connection with a WebSocket is closed
socket.onclose = function(event) {
  onSocketClose(event);
};

// Fired when a connection with a WebSocket has been closed because of an error
socket.onerror = function(event) {
  onSocketError(event);
};
```

## Instalación de Socket.io

Para facilitar la gestión de sockets, utilizaremos la librería `socket.io` .

### Backend

Deberemos añadir este código en nuestro express:

```
//require HTTP library
const http = require("http");

// Create HTTP server
const server = http.createServer(app);

const { Server } = require("socket.io");
const io = new Server(server, {
    cors: {
      origin: '*',
      methods: ['GET', 'POST'],
    },
  });
```

y cambiaremos el listen() al nuevo servidor HTTP que hemos creado

```
server.listen(port, () => {
  console.log("WEB SERVER listening on *:" + port);
});
```

¿Por qué debemos crear un servidor HTTP? ¿Express no es ya un servidor HTTP?

Express es un servidor HTTP, pero cuando hacemos un `app.listen()` realmente estamos ejecutando el siguiente código:

```
const http = require("http");
http.createServer(app).listen()
```

### Frontend

Deberemos llamar a la biblioteca mediante el siguiente script

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/4.6.1/socket.io.min.js"></script>
```

y luego iniciar el socket mediante este script apuntando a nuestro servidor:

```
<script>
    var socket = io('http://localhost:3000');
</script>
```

## Detectar conexión del websocket

Los eventos en Socket.io se gestionan de una forma similar a los `addEventListener` de Javascript

```
io.on('connection', (socket) => {
    console.log('a user connected');
  });
```

## Detectar desconexión del websocket

Una vez conectado el canal, utilizaremos el `socket` para gestionar las emisiones entre clientes y servidor.

```
io.on('connection', (socket) => {
    console.log('a user connected');
    socket.on('disconnect', () => {
      console.log('user disconnected');
    });
  });
```

## Recepción de un evento en el servidor y envío de eventos

En socket.io tenemos la posibilidad de crear diferentes tipos de eventos. En este caso, hemos creado un tipo "message" para controlar los mensajes del chat.
Además, con el método `io.emit` enviamos un mensaje a todos los que están conectados en el socket.

```
io.on('connection', (socket) => {

    console.log('a user connected');

    socket.on('disconnect', () => {
        console.log('user disconnected');
        });

    socket.on('message', (msg) => {
        console.log('message: ' + msg);
        io.emit('message', msg);
        });

  });
```

Una vez estamos emitiendo el evento desde nuestro servidor, debe poder recibirse en nuestros clientes. Lo haremos de la siguiente forma:

```
socket.on('message', function(msg){
    ...
})
```

## Ideas para mejorar la aplicación

- Enviar un mensaje a todo el mundo cuando un usuario desconecta
- Soporte para nicknames
- Añadir funcionalidad "{usuario} está escribiendo…"
- Mostrar quién está online
- Añadir mensajería privada
