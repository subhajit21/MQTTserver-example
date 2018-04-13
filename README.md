# MQTTserver-example
Here we just send a message on a particular topic and subscribers can see that message.
#include<ESP8266WiFi.h>
#include<PubSubClient.h>
//#include<WiFiManager.h>//comment out if want to use NodeMCU as a hotspot and comment out rest of commented lines

//using mobile hotspot 
const char* ssid="....";//hotspot id name
const char * pass="......";//hotspot password
const char* mqtt_server="broker.hivemq.com";//broker name

WiFiClient espclient;
PubSubClient client(espclient);
long lastMsg=0;
char msg[50];
int value=0;

void setup() {
  Serial.begin(115200);
 // WiFiManager wifimanager;
  //wifimanager.autoConnect("NodeMCU");
  pinMode(BUILTIN_LED,OUTPUT);
  
  setup_wifi();
  client.setServer(mqtt_server,1883);
  client.setCallback(callback);

}
void setup_wifi(){
   Serial.println();
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, pass);
 
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected");
  
  pinMode(BUILTIN_LED, OUTPUT);
  
}

void callback(char *topic,byte* payload,unsigned int length){
  Serial.print("msg arraived");
  Serial.print(topic);
  Serial.print("]");
  for(int i=0;i<length;i++)
  {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  if((char)payload[0]=='on')
    {
        digitalWrite(BUILTIN_LED,LOW);
    
    }
  else
    {
     digitalWrite(BUILTIN_LED,HIGH);  
    
  }
}
void reconnect(){
  while(!client.connected()){
    Serial.print("attempting mqtt connection");
    if(client.connect("ESP8266Client")){
      Serial.println("connect");
      //client.publish("rishil/rishil","hello world");//comment out if want to publish payload in to the suscribe side
      client.subscribe("sit/room1");
  
  }else{
      Serial.println("failed,rc=");
       Serial.print("try agian");
       delay(500);
    
    }
  }
}
void loop() {
  if(!client.connected()){
    reconnect();
  }
  client.loop();
  long now=millis();
  if(now-lastMsg>2000)
  {
    lastMsg=now;
    ++value;
    snprintf(msg,75,"hello world#1d",value);//snprintf makes a buffer
    Serial.print("publish msg");
    Serial.print(msg);
    client.publish("rishi/rishi",msg);
  }

}
