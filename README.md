# from-sound-to-draw
processing code to make a pattern from sound input


import fontastic.*;

import krister.Ess.*;


int bufferSize;
int steps;
float limitDiff;
int numAverages=32;
float myDamp=.1f;
float maxLimit,minLimit;
SoundLayer lowLayer0;
SoundLayer lowLayer1;
SoundLayer midLayer0;
SoundLayer midLayer1;
SoundLayer highLayer0;
SoundLayer highLayer1;

//render type switch
boolean block = true;

FFT myFFT;
AudioInput myInput;

void setup () {
 size(1000,800);
 smooth();
 // start up Ess
 Ess.start(this);  

 // set up our AudioInput
 bufferSize=512;
 myInput=new AudioInput(bufferSize);

 // set up our FFT
 myFFT=new FFT(bufferSize*2);
 myFFT.equalizer(true);

 // set up our FFT normalization/dampening
 minLimit=.005;
 maxLimit=.05;
 myFFT.limits(minLimit,maxLimit);
 myFFT.damp(myDamp);
 myFFT.averages(numAverages);

 // get the number of bins per average 
 steps=bufferSize/numAverages;

 // get the distance of travel between minimum and maximum limits
 limitDiff=maxLimit-minLimit;

 frameRate(25);       

 lowLayer0 = new SoundLayer(); 
 lowLayer0.lowPoint = 50;
 lowLayer0.highPoint = 100;
 lowLayer0.x = 10;
 lowLayer0.y = 10;
 lowLayer0.objectID = 0;
 
 lowLayer1 = new SoundLayer(); 
 lowLayer1.lowPoint = 100;
 lowLayer1.highPoint = 150;
 lowLayer1.x = 10;
 lowLayer1.y = 10;
 lowLayer1.objectID = 1;

 midLayer0 = new SoundLayer(); 
 midLayer0.lowPoint = 150;
 midLayer0.highPoint = 200;
 midLayer0.x = 10;
 midLayer0.y = 10;
 midLayer0.objectID = 2;
 
 midLayer1 = new SoundLayer(); 
 midLayer1.lowPoint = 200;
 midLayer1.highPoint = 250;
 midLayer1.x = 10;
 midLayer1.y = 10;
 midLayer1.objectID = 3;
 

 highLayer0 = new SoundLayer(); 
 highLayer0.lowPoint = 250;
 highLayer0.highPoint = 300;
 highLayer0.x = 10;          //offets the line
 highLayer0.y = 10;
 highLayer0.objectID = 4;
 
 highLayer1 = new SoundLayer(); 
 highLayer1.lowPoint = 300;
 highLayer1.highPoint = 500;
 highLayer1.x = 10;          //offets the line
 highLayer1.y = 10;
 highLayer1.objectID = 5;
 
 
 myInput.start();

 myInput.start();
}

void draw()
{
 //background(0);
Fontastic f = new Fontastic(this, "WormFont");

f.setAdvanceWidth(1000);


PVector[] points= new PVector[6];

points[0] = new PVector(0, 0);
points[1] = new PVector(0, 200);
points[2] = new PVector(800, 1000);
points[3] = new PVector(1000, 1000);
points[4] = new PVector(1000, 800);
points[5] = new PVector(200, 0);

PVector[] points1 = new PVector[6];
points1[0] = new PVector(1000, 0);
points1[1] = new PVector(800, 0);
points1[2] = new PVector(0, 800);
points1[3] = new PVector(0, 1000);
points1[4] = new PVector(200, 1000);
points1[5] = new PVector(1000, 200);

PVector[] points2 = new PVector[6];
points2[0] = new PVector(0, 500);
points2[1] = new PVector(200, 640);
points2[2] = new PVector(800, 640);
points2[3] = new PVector(1000, 500);
points2[4] = new PVector(800, 360);
points2[5] = new PVector(200, 360);

PVector[] points3 = new PVector[6];
points3[0] = new PVector(500, 0);
points3[1] = new PVector(360, 200);
points3[2] = new PVector(360, 800);
points3[3] = new PVector(500, 1000);
points3[4] = new PVector(640, 800);
points3[5] = new PVector(640, 200);

PVector[] points4 = new PVector[0];


f.addGlyph('0').addContour(points);
f.addGlyph('1').addContour(points1);
f.addGlyph('2').addContour(points2);
f.addGlyph('3').addContour(points3);
f.addGlyph(' ').addContour(points4);

f.buildFont();
PFont myFont = createFont(f.getTTFfilename(), 64);

size(1000, 800);
background(0);
fill(255);
textFont(myFont);
 lowLayer0.update();
 lowLayer1.update();
 midLayer0.update();
 midLayer1.update();
 highLayer0.update();
 highLayer1.update();

}

void mousePressed()
{
 block = false;
}
void mouseReleased()
{
 block = true;
}

public void audioInputData(AudioInput theInput)
{
 myFFT.getSpectrum(myInput);
}

class SoundLayer
{
 int lowPoint = 0;
 int highPoint = 512;
 int edginess = 0;
 int x = 0;
 int y = 0;
 int objectID = 0;
 boolean show = true;
 float threshold = 0.10;


 void update()
 {

   translate(x, y);

   //looping through only this layer's portion of the sound buffer
   for (int i= lowPoint; i<highPoint; i++)
   {
     float left=160;
     float right=160;
     float amplitude = myFFT.spectrum[i]; //amp at this frequency
     float r = random(-200, 255);
     left-=amplitude*2050.0; //650 changes the size growth of the spectrum lines
     right-=amplitude*2050.0;

     if(frameCount % 30 == 0)
     {
       println(amplitude);
       println("LEFT : " + left);
       println("RIGHT: " + right);
     }


     if(amplitude < threshold)
     {
       show = false; 
     }
     else
     {
       show = true;
     }

     //DRAWS THE VISUALIZATION
     if(show)
     {
       if (objectID == 0)
       {
         //DRAW THE LOW0
        text(" 223301302023", width*0.02, height*0.15); 
       }
       else if (objectID == 1)
       {
         //DRAW THE LOW1
         text("201320310232", width*0.02, height*0.25); 
       }
       else if(objectID == 2)
       {
         //DRAW THE MID0
         text("020030212012", width*0.02, height*0.35);
       }  
         else if (objectID == 3)
       {
         //DRAW THE MID1
         text("112311120320", width*0.02, height*0.45);
       }
       
        else if(objectID == 4)
       {
         //DRAW THE HIGH0
         text("201020120103", width*0.02, height*0.55);
       }
       
      else if(objectID == 5)
       {
         //DRAW THE HIGH0
         text("330201202001", width*0.02, height*0.65);
       }

     }    
   }
 }
}
