# The Serial implementation

*This is a draft of something that can't work for us, due to the speed
limitation of Minitel's serial port. It can still be relevant for other
terminals that provide a faster serial port (38400 baud when possible)*

## Software

Working first on the software sounded like the right thing to do before buying
old hardware. The idea was to prove the feasability of the concept with
standard computer screens with a fullscreen terminal emulator.

### An easy audio reactive setup?

At first, the idea was to code some tools to display ASCII geometry in a
terminal (something like a `ncurses` / `cli` fullscreen app). But just this
part would require a lot of work for us and the priority was to be able to
display characters that move in sync with the music. Having to re-implement
MIDI/Audio/Sync parts in a custom tool didn't sound so great. After looking for
frameworks that could help reduce the amount of work, we realized it was
something easier to accomplish with software we already know and use (like
TouchDesigner as an example). Easy and efficient, we just needed a way to
convert video as ASCII text.

### Video to Text?

This is something quite old. Have you heard about `libcaca` and `aalib`?
`libcaca` was created by [Sam
Hocevart](https://en.wikipedia.org/wiki/Sam_Hocevar) in 2003. It's powerful and
allows to encode video with ASCII chars and colors. `aalib` is quite similar and
a bit older, it just works for black and white but allows to tweak dithering,
brightness, contrast... For colorless terminals, aalib is perfect.

In TouchDesigner, [you can stream any video object with RTSP / SRT
protocols](https://docs.derivative.ca/Video_Stream_Out_TOP). With [mplayer](http://www.mplayerhq.hu/design7/news.html) on
linux, you can connect to any RTSP and transcode it using `aalib` or `libcaca`.

Lets say we want to encode a Video_Stream_Out_TOP in ASCII:

```
CACA_DRIVER=ncurses mplayer -vo caca -really-quiet -framedrop rtsp://127.0.0.1:9090/stream 2>/dev/null
```

WIP

### Encode once, display everywhere

WIP

## Hardware

### The computer

A cheap way to build a portable setup is to use a Raspberry Pi or any clone for
the computer part.

### The Minitel's DIN interface

To use the DIN interface as a serial port you have to be careful of the
following points:

- The serial port uses [TTL
  protocol](https://www.seeedstudio.com/blog/2019/12/11/rs232-vs-ttl-beginner-guide-to-serial-communication/)
  (Transistor-Transistor Logic != RS232)

- For serial communication, 3 of the 5 pins of the DIN interface are useful:
  **Rx**, **Tx**, **GND**

- In classic TTL serial communications: (!= Minitel)

  ```
  Logical state 0 = 0V
  Logical state 1 (Vcc) = 3.3V or 5V
  ```

  In the Minitel world:

  ```
  min    state       max
  ---------------------------------------------------------
  Rx & PT(in)
    0V < logical 0 < 0.4V  (Consumed current I < 150µA)
  2.5V < logical 1 <  15V  (Interface resistance R < 800Ω)

  Tx & PT(out)
    0V < logical 0 < 0.4V
  2.5V < logical 1 <  15V  (Interface resistance R = 200kΩ)
  ```

  Minitel uses [open-collector](https://en.wikipedia.org/wiki/Open_collector).

  ([Source](http://pila.fr/wordpress/?p=361) + Official documentation)

- If you use a PL2303 USB to Serial adapter, you need to protect it from the Rx
  voltage.

WIP

## Abort mission

On [this
post](https://vanschklift.com/blog/post/2020/05/21/Yet-Another-Minitel-Project)
you can check the actual speed of the serial communication. There is also a
small demo of the rendering of a Youtube video with libcaca, which is similar
to what we planned to do. This is non-usable in a audio reactive setup!
