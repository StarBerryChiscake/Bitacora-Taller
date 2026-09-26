
info importante: 
-
Pines que debes evitar:
GPIO 6 a 11: conectados a la memoria interna. Usarlos bloquea la placa.
GPIO 34, 35, 36 y 39: solo sirven como entrada, no pueden encender un LED.
Pin Uso en esta guía Por qué este pin
GPIO 23 Salida hacia el LED Pin libre, sin funciones especiales al arrancar
GPIO 4 Entrada del pulsador Pin libre, admite resistencia pull-up interna
GND Tierra (0 V) Cierra todos los circuitos
3V3 Alimentación 3,3 V No se usa en estos ejercicios
Guía ESP32: Blink, Pulsador y LED por WiFi
Page 3 of 15
GPIO 0, 2, 12 y 15: afectan el arranque de la placa. Úsalos solo si sabes lo que haces.

Ejercicio 1 
-

``` 
//Ejercicio 1: Blink (Parpadeo)
// Enciende y apaga un LED cada 1 segundo.

// Declaraciones antes de empezar el setup 
const int PIN_LED = 23; // pin donde esta conectado el LED
const int TEMPO = 1000; // tiempo en milisegundos (1000 ms = 1 s) 
// Inicio del programa. 
void setup() {
pinMode(PIN_LED, OUTPUT); //El pin 23 se declara como SALIDA
}
// Inicio del bucle
void loop () {
digitalWrite(PIN_LED, HIGH); //3,3 V en el pin: LED encendido
delay(TEMPO); // Esperar 1 segundo/ Retraso de tiempo 1 segundo
digitalWrite(PIN_LED, LOW); // 0 V en el pin: LED apagado
delay(TEMPO); // Esperar 1 segundo/ Retraso de tiempo 1 segundo
}
// Termino del programa y el bucle.
``` 

Ejercicio 2 
-
```
//Ejercicio 2: LED con pulsador (botón de encendido y apagado)
// El LED se enciende mientras el botón está presionado.

// Declaraciones antes de empezar el setup
const int PIN_LED = 23; // Salida: LED
const int PIN_BOTON = 4; // Entrada: pulsador
// Inicio del programa.
void setup() {
pinMode(PIN_LED, OUTPUT); // El LED es una SALIDA
pinMode(PIN_BOTON, INPUT_PULLUP); // El botón es una ENTRADA con pull-up interna
Serial.begin(115200); // pare ver el mensaje en el monitor serie
}
void loop() {
// 1. LEER
int estadoBoton = digitalRead(PIN_BOTON);
// 2. DECIDIR y 3. ACTUAR
// Inicio de condicionales.
if (estadoBoton == LOW) { // LOW = presionado (Por la pull-up) 
digitalWrite(PIN_LED, HIGH); digitalWrite(PIN_LED, HIGH);
Serial.println("Botón presionado: LED encendido");
} else {
digitalWrite(PIN_LED, LOW);
} //Termino de condicionales.
delay(10); // Pausa breve para no saturar el monitor serie
} // Termino del programa.

```

Ejercicio 3   
-
Info importante para validar el ejercicio:
Entregas el prompt (o prompts) que usaste.
El LED está en GPIO 23, se usa pinMode , digitalWrite y constantes con nombre,
como en los ejercicios anteriores.
El loop() no tiene delay() : la placa debe estar siempre atenta a nuevos pedidos.
Cambiaste el nombre de la red por el de tu grupo, para no confundirte con las demás.
Puedes explicar en voz alta qué hace cada línea. El profesor puede preguntarte por
cualquiera.


promt de IA: 
Escribe un sketch de Arduino para ESP32 (Arduino IDE, core de Espressif 3.x). La
ESP32 debe crear una red WiFci en modo Access Point llamada "ESP32-Grupo01" con
clave "diseno2026". Debe levantar un servidor web con la librería WebServer.h en el
puerto 80. La ruta "/" muestra una página HTML adaptada a celular con dos botones,
Encender yApagar, y el estado actual del LED. Las rutas "/encender" y "/apagar"
controlan un LED en el GPIO 23 con digitalWrite y redirigen a "/". Imprime la IP en el
Monitor serie a 115200 baudios. No uses delay() dentro de loop(). Comenta cada
bloque en español. (ejemplo dado por el profe). 

Resultado: (Decidimos rendirnos y escribir a mano el codigo ya que es mas dificil revisar que esta mal con  este codigo de ia 😢)
-
```
// Ejercicio 3: LED controlado desde un a pagina web
// La ESP crea su propia red wifi y sirve una pagina con dos botones.

#include <WiFi.h> //Funciones de wifi 
#include <WebServer.h> // Servidor Web simple 

// Definición del pin del LED
const int PIN_LED = 23;

// Configuración del Servidor Web en el puerto 80
WebServer server(80); 

// Configuración de las credenciales del Access Point (AP) 
const char* ap_ssid = "ESP32-Grupo01";
const char* ap_password = "diseno2026";

// Función para generar la página HTML adaptada a dispositivos móviles
String generarPaginaHTML() {
  // Leemos el estado actual del LED (HIGH o LOW)
  bool estadoLED = digitalRead(PIN_LED);

  String html = "<!DOCTYPE html><html lang='es'><head>";
  html += "<meta charset='UTF-8'>";
  // Meta tag viewport para adaptabilidad a pantalla de celulares
  html += "<meta name='viewport' content='width=device-width, initial-scale=1.0'>";
  html += "<title>Control ESP32</title>";
  
  // Estilos CSS integrados para una interfaz móvil limpia e intuitiva
  html += "<style>";
  html += "body { font-family: Arial, sans-serif; text-align: center; background-color: #f4f4f9; margin: 0; padding: 20px; }";
  html += ".card { background: white; padding: 20px; border-radius: 12px; box-shadow: 0 4px 8px rgba(0,0,0,0.1); max-width: 400px; margin: auto; }";
  html += "h1 { color: #333; font-size: 24px; margin-bottom: 20px; }";
  html += ".state { font-size: 18px; font-weight: bold; margin-bottom: 25px; }";
  html += ".on { color: #28a745; } .off { color: #dc3545; }";
  html += ".btn { display: block; width: 100%; padding: 15px 0; margin: 10px 0; font-size: 18px; color: white; border: none; border-radius: 8px; text-decoration: none; cursor: pointer; transition: 0.2s; }";
  html += ".btn-on { background-color: #28a745; } .btn-on:active { background-color: #218838; }";
  html += ".btn-off { background-color: #dc3545; } .btn-off:active { background-color: #c82333; }";
  html += "</style></head><body>";

  html += "<div class='card'>";
  html += "<h1>Control de LED</h1>";
  
  // Muestra el estado del LED dinámicamente
  if (estadoLED) {
    html += "<p class='state'>Estado del LED: <span class='on'>ENCENDIDO</span></p>";
  } else {
    html += "<p class='state'>Estado del LED: <span class='off'>APAGADO</span></p>";
  }

  // Botones de control que envían solicitudes GET
  html += "<a href='/encender' class='btn btn-on'>Encender</a>";
  html += "<a href='/apagar' class='btn btn-off'>Apagar</a>";
  html += "</div>";

  html += "</body></html>";
  return html;
}

// Manejador para la ruta principal "/"
void handleRoot() {
  server.send(200, "text/html", generarPaginaHTML());
}

// Manejador para encender el LED en la ruta "/encender"
void handleEncender() {
  digitalWrite(PIN_LED, HIGH);
  // Redirección HTTP 303 a la raíz
  server.sendHeader("Location", "/");
  server.send(303);
}

// Manejador para apagar el LED en la ruta "/apagar"
void handleApagar() {
  digitalWrite(PIN_LED, LOW);
  // Redirección HTTP 303 a la raíz
  server.sendHeader("Location", "/");
  server.send(303);
}

void setup() {
  // Inicialización del Puerto Serie a 115200 baudios
  Serial.begin(115200);
  delay(100);

  // Configuración del GPIO 23 como salida
  pinMode(PIN_LED, OUTPUT);
  digitalWrite(PIN_LED, LOW); // Estado inicial: Apagado

  // Configuración e inicio del punto de acceso (Access Point)
  Serial.println("\nIniciando punto de acceso Wi-Fi...");
  WiFi.mode(WIFI_AP);
  WiFi.softAP(ap_ssid, ap_password);

  // Impresión de la dirección IP asignada al AP
  IPAddress myIP = WiFi.softAPIP();
  Serial.print("Red AP iniciada. Conéctate a la SSID: ");
  Serial.println(ap_ssid);
  Serial.print("Dirección IP del servidor web: ");
  Serial.println(myIP);

  // Asignación de rutas al Servidor Web
  server.on("/", handleRoot);
  server.on("/encender", handleEncender);
  server.on("/apagar", handleApagar);

  // Inicio del servidor web
  server.begin();
  Serial.println("Servidor HTTP iniciado correctamente.");
}

void loop() {
  // Atiende las peticiones entrantes de los clientes web de forma no bloqueante (sin delay)
  server.handleClient();
}
```

CODIGO DE REFERENCIA (entregado por el profe) (escrito a mano para practicar) 
-
```
//Ejercicio 3: LED controlado desde página web
//La ESP32 crea su propia red wifi y sirve una páguina con 2 botones

#include <WiFi.h> // funciones de wifi
#include <WebServer.h> // servidor de web simple

// CONFIGURACIÓN
const char* NOMBRE_RED = "ESP32-Javiera";  // Cambia por el nombre de tu
grupo (sin ñ ni tildes)
const char* CLAVE_RED = "";  // Mínimo 8 caracteres
const int PIN_LED = 23;

WebServer servidor(80); // Servidor en el puerto 80 (HTTP)

``` 

// ---- Configuración ----

WebServer servidor(80); // Servidor en el puerto 80 (HTTP)
bool ledEncendido = false; // Recuerda el estado actual del LED
// Construye la página HTML según el estado del LED
String crearPagina() {
String estado = ledEncendido ? "ENCENDIDO" : "APAGADO";
String html = "<!DOCTYPE html><html lang='es'><head>";
html += "<meta charset='UTF-8'>";
html += "<meta name='viewport' content='width=device-width, initialscale=1'>";
html += "<title>Control LED ESP32</title>";
html += "<style>";
html += "body{font-family:sans-serif;textalign:center;padding:40px;background:#111;color:#eee;}";
html += "a{display:block;margin:16px auto;padding:20px;width:200px;borderradius:12px;";
html += "font-size:22px;text-decoration:none;color:#fff;}";
html += ".on{background:#2e7d32;} .off{background:#c62828;}";
html += "</style></head><body>";
html += "<h1>LED ESP32</h1>";
html += "<p>Estado: <strong>" + estado + "</strong></p>";
html += "<a class='on' href='/encender'>Encender</a>";
html += "<a class='off' href='/apagar'>Apagar</a>";
html += "</body></html>";
return html;
}
// Redirige el navegador de vuelta a la página principal
void volverAlInicio() {
servidor.sendHeader("Location", "/");
servidor.send(303);
Guía ESP32: Blink, Pulsador y LED por WiFi
}
// Ruta "/": muestra la página
void paginaPrincipal() {
servidor.send(200, "text/html", crearPagina());
}
// Ruta "/encender"
void encenderLed() {
ledEncendido = true;
digitalWrite(PIN_LED, HIGH);
volverAlInicio();
}
// Ruta "/apagar"
void apagarLed() {
ledEncendido = false;
digitalWrite(PIN_LED, LOW);
volverAlInicio();
}
void setup() {
Serial.begin(115200);
pinMode(PIN_LED, OUTPUT);
digitalWrite(PIN_LED, LOW); // Partir con el LED apagado
// Crear la red WiFi propia (modo Access Point)
WiFi.softAP(NOMBRE_RED, CLAVE_RED);
Serial.print("Red creada: ");
Serial.println(NOMBRE_RED);
Serial.print("Abre en el navegador: http://");
Serial.println(WiFi.softAPIP()); // Normalmente 192.168.4.1
// Asociar cada ruta con su función
servidor.on("/", paginaPrincipal);
servidor.on("/encender", encenderLed);
servidor.on("/apagar", apagarLed);
servidor.begin();
}
void loop() {
servidor.handleClient(); // Atender pedidos del navegador. Sin delay().
}

Glosario 
-
HTTP : HTTP significa Protocolo de transferencia de hipertexto y es la forma en que diferentes partes de Internet se comunican entre sí. HTTP es lo que se conoce como un lenguaje de "solicitud-respuesta" porque el explorador web (Firefox, Safari, etc.).


