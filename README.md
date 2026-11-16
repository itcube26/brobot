# bro_bot
Прототип робота с манипулятором


# Оборудование

- Тележка с 2-мя моторами
    - VexV5 Arduino Shield (Mega2560 <--> Serial2 <--> ESP32)
    - VexV5 Motor With Encoder
    - Металлический каркас - заднеприводный (VEX5_PORT_1 - правое колесо, VEX5_PORT_6 - левое)

- Манипулятор уловой с 6 моторами Dynamixel.
- android смартфон с камерой, gps, wifi, акселерометром и прочим
- Ноутбук с Ubuntu 26.04
- Wifi с именем POISK_LAN

# Примеры кода

- Движение вперед мотором VexV5 https://github.com/vex-ru/VexV5

```cpp
#include <Vex5.h>

#define BAUDRATE         (500000) //SPI speed
#define SPI_NSS_PIN      (-1)     //Use jumper
#define IS_RESET_SHIELD  (0)      //Keep servo positions during reset

Vex5_Motor motor;
int32_t goalPosition = 0;
int16_t maxSpeed = 200;
int16_t maxCurrent_mA = 0; //0 - unlimited
int16_t realSpeed = 0;
int32_t realPosition = 0;
int16_t realPower = 0;

unsigned long time = millis();

void setup() {
  Serial.begin(115200);
  Vex5.begin(BAUDRATE, SPI_NSS_PIN, IS_RESET_SHIELD);
  motor.begin(VEX5_PORT_1);
  motor.resetEncoder();
  time = millis();
}

void loop() {
  if(millis() - time > 1000 && realSpeed == 0) {
    time = millis();
    goalPosition = goalPosition + Vex5.deg_to_ticks(90);
    motor.setMaxCurrent(maxCurrent_mA);
    motor.setPosition(goalPosition, maxSpeed);
  } 
  motor.getSpeed(realSpeed);
  motor.getPosition(realPosition);
  delay(10);
  motor.getPower(realPower);
  Serial.print("Goal Position:\t" + String(goalPosition) + "\t");
  Serial.println("Real Position Speed Power:\t" + String(realPosition) + "\t" + String(realSpeed) + "\t" + String(realPower));
  delay(300);
}
```



- Общение между Mega2560 и ESP32 с прошивкой Bluetooth2Serial для ESP32

```cpp
void setup() { 
    Serial.begin(115200); 
    Serial2.begin(115200);
}

void loop() {
    if (Serial2.available()) { 
        Serial.write(Serial2.read());
    }
}
```

