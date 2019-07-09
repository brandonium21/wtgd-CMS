The Witch is a retroPie build. Now, I know everyone builds one and that's why I had to build mine. And it just so happened that I pulled inspiration from the Nintendo Switch(No relation).

I ended up overclocking my Raspberry pi 3 to 1.3 gHz which required me to incorporate a salvaged Macbook pro 2012 fan. Now I am not sure but I think I'm the first to put a fan in my portable Retropie build but who's bragging?

This all came about when my boss said someone donated a 9 in LCD to the lab; I figured I would find someway to use it.

There was a lot of interesting parts to this project and Im happy to share them with you.

![Imgur](https://i.imgur.com/VYCftAM.jpg)

#### THE REDESIGN:
I had decided to do it right by completely building it out in Fusion360 then making it in reality but it was ugly, clunky and heavy. I pivioted twards a design with less plastic and more intergrated components.

#### THE ANALOG STICK:
Now Retropie only accepts digital inputs. I was going to write a script that ran on boot to interpret the analog signals to keyboard clicks. But I figured that would be overkill and might slow down performance. Instead I used an Adafruit trinket Pro 3v due to its size and amount of GPIO. The Trinket converted the analog inputs in a threshold to digital outputs and I then tied those to raspberry pi pins and mapped those to keyboard clicks. It was painful but it worked very well. My only problem is that I am using the ADC and that takes an amount of mA that im uncompfortable with,

![Imgur](https://i.imgur.com/3TVwDAI.jpg)

I Wrote a simple Arduino sketch to acheve this.

```
int joyPin1 = 0;                 // slider variable connecetd to analog pin 0
int joyPin2 = 1;                 // slider variable connecetd to analog pin 1
int value1 = 0;                  // variable to read the value from the analog pin 0
int value2 = 0;                  // variable to read the value from the analog pin 1

const int left = 13;
const int right = 12;
const int up = 11;
const int down = 10;

void setup() {
  pinMode(left, OUTPUT);
  pinMode(right, OUTPUT);
  pinMode(up, OUTPUT);
  pinMode(down, OUTPUT);
  digitalWrite(left, HIGH);
  digitalWrite(right, HIGH);
  digitalWrite(up, HIGH);
  digitalWrite(down, HIGH);
}

void loop() {
  // reads the value of the variable resistor 
  value1 = analogRead(joyPin1);   
  // this small pause is needed between reading
  // analog pins, otherwise we get the same value twice
  delay(100);             
  // reads the value of the variable resistor 
  value2 = analogRead(joyPin2);

  if (value1 == 0){
    digitalWrite(up, LOW);
    }
  else if (value1 == 1023){
    digitalWrite(down, LOW);
    }
  else if (value2 == 0){
    digitalWrite(left, LOW);
    }
  else if (value2 == 1023){
    digitalWrite(right, LOW);
    }
  else{
    digitalWrite(right, HIGH);
    digitalWrite(left, HIGH);
    digitalWrite(up, HIGH);
    digitalWrite(down, HIGH);
    }
}
```
#### THE STANDOFFS:

Back in the day I had an idea for clapping to faces together invisibly, but keeping it serviceable. The design is two complementary rabbits with a hole through them. That way a screw kept them together. After gluing both rabbits to there respective faces I drove a m4 screw threw them both to clamp the Witch together.

![Imgur](https://i.imgur.com/GPMa4pq.jpg)

This was a very fun project and later I will make a custom carrying case using EVA foam. Should be FUN!

![Imgur](https://i.imgur.com/TufhlEx.jpg)

<iframe width="560" height="315" src="https://www.youtube.com/embed/2kJZ6NeeW8o" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

![Imgur](https://i.imgur.com/J1n6QNr.jpg)

![Imgur](https://i.imgur.com/FS1CPvv.jpg)

![Imgur](https://i.imgur.com/irpg7yz.jpg)

![Imgur](https://i.imgur.com/ykHPVp3.jpg)

![Imgur](https://i.imgur.com/s5EoRCb.jpg)

![Imgur](https://i.imgur.com/4zGJwNL.jpg)
