# task-10.5
#include <Wire.h>
const int MPU6050_ADDRESS = 0x68; 
const int POWER_MANAGEMENT_REGISTER = 0x6B;  
const int GYROSCOPE_Z_AXIS_HIGH_BYTE_REGISTER = 0x47;  
const float GYROSCOPE_SENSITIVITY_SCALE_FACTOR = 131.0;  
float currentYawAngle = 0.0; 
unsigned long previousUpdateTime = 0;  

void setup() {
  Wire.begin();
  Serial.begin(9600);
  Wire.beginTransmission(MPU6050_ADDRESS);
  Wire.write(POWER_MANAGEMENT_REGISTER);
  Wire.write(0);
  Wire.endTransmission(true);
}


void loop() {
  unsigned long currentTime = millis();
  float deltaTime = (currentTime - previousUpdateTime) / 1000.0;
  int16_t gyroscopeZAxisData = readGyroscopeZAxisData();
  
  float gyroscopeZAxisRate = gyroscopeZAxisData / GYROSCOPE_SENSITIVITY_SCALE_FACTOR;
  
  currentYawAngle += gyroscopeZAxisRate * deltaTime
  
  Serial.print("Yaw: ");
  Serial.println(currentYawAngle);
  previousUpdateTime = currentTime;
  delay(100);
}

int16_t readGyroscopeZAxisData() {
  int16_t gyroscopeZAxisData;
  Wire.beginTransmission(MPU6050_ADDRESS);
  Wire.write(GYROSCOPE_Z_AXIS_HIGH_BYTE_REGISTER);
  Wire.endTransmission(false);
  Wire.requestFrom(MPU6050_ADDRESS, 2, true);
  
  gyroscopeZAxisData = Wire.read() << 8 | Wire.read();
  
  return gyroscopeZAxisData;
}
