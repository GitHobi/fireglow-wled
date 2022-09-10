# Fire glow for WLED
A natural looking fire effect for wled

Goal was to have a halfway realistic looking fire glow, as usually emited by a burning gas lamp / gas tourch.

![Sample](https://user-images.githubusercontent.com/7893927/189481771-66683749-7cf1-43bb-89c9-03c3fc735907.gif)

## How to ...

Everything is based on [WLED](https://kno.wled.ge/), running on a *Wemos D1 mini* and a small powerbank as power supply. 

Connected is a standard RGB led stripe, cutted down to 4 segments, with 9 leds each, 36 in total.
![IMG_2879](https://user-images.githubusercontent.com/7893927/189482083-fd58ed32-5dd6-48ff-8acc-65ba0b116975.JPG)

You can use less segments, but in my opinion the effect works best with 3-4 segments!

## Compile the custom effect

WLED comes with some predefined fire like effects. In my opinion they are "nice", but do not provide a realistic fire effect. I created a custom effect, which - in my opinion - looks more realistic.

How to create custom effects is described [here](https://kno.wled.ge/advanced/custom-features/#create-custom-effects). In essence you need to checkout the source code and integrate the below effect to the *fx.cpp* and *fx.h* files, compile everything and upload it to your Wemos D1. 

```C++
uint16_t WS2812FX::mode_fireglow(void)
{
  fade_out(SEGMENT.speed);

  if (SEGENV.aux0 < 2)
  {
    SEGENV.aux0++;
  }
  else
  {
    SEGENV.aux0 = 0;
    auto i = random8(SEGLEN / 2);
    setPixelColor(i, color_from_palette(SEGLEN - i-1, true, PALETTE_SOLID_WRAP, 255));

    if ( SEGENV.aux1 == 0 )
    {
      SEGENV.aux1 = random8() > 240 ? 1 : 0;
    }
  }

  if ( SEGENV.aux1 == 1 )
  {
    setPixelColor(SEGENV.step, color_from_palette(SEGLEN - SEGENV.step, true, PALETTE_SOLID_WRAP, 255));
    SEGENV.step++;
    if (SEGENV.step+1> SEGLEN)
    {
      SEGENV.step = SEGLEN / 2 ;
      SEGENV.aux1 = 0;
    }
  }
  return FRAMETIME;
}
```

The code is actually rather simple, some hints, if you want to play with it on your own:
* The effect uses a pallete you have selected. Suitible pallets are "fire" and "icefire". 
* *fade_out* is used to slowly distinguish all lit leds. I'm using the value from *SEGMENT.speed" - but actually you'll want to use rather high values (>200) for a good effect. 
* the lower 50% (*SEGLEN/2*) of the segment are ranomly lit with the lightes colors of the palette ... for the fire palettes these are the white and yellowish colors. This simulates "hot" and "vivid" burning at the lower end of the segments. 
* Every now and then a blast of fire is allowed to rise up to the upper part of a segment. This is triggered by a random number and *SEGENV.aux1* as switch. 
* If *SEGENV.aux1* is 1 a blast rises up, which in the end is nothing more than lighting the upper LEDs of the segment with the "colder" colores (dark reds), taken from the palette. This gives a nice fade-out effect for the fire. 

### Configuring WLED

After you have everything compiled and transfered you want to make some settings:
* Create your segments.
* Assign all segments the effect + the (same) palette
* Set the effect speed to some high value!
* You want to play with the brightness of the effect ... lower brightness gives more realistic effects.
* Create a profile.
* Assign the profile as start-up profile. 

### Housing

I used a an old candle as housing.
You prop. will need to use some foile or paper as "diffusor" - this enhances the effect! In any case you should not expose the LEDs directly, this sort of destroyes the effect ...
![IMG_2880](https://user-images.githubusercontent.com/7893927/189482709-8c564cf5-b93a-46fa-b333-cf843a37eafd.JPG)




