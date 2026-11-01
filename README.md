# Ghostwire

Text chat between phones over sound. Messages are sent as near-ultrasonic tones
(17.5–19.3 kHz) from one phone's speaker and decoded by the other phone's mic.
No Wi-Fi or Bluetooth link between the phones.

## How it works

- 19 sine tones: 17 carry data, plus a START and an END tone.
- Each byte is split into two 4-bit nibbles. A nibble `v` is sent as tone
  `(previous + 1 + v) % 17`, so consecutive symbols are always different tones
  and the receiver can tell them apart without a shared clock.
- Packet: `[length, ...UTF-8 payload, CRC-8]`, 90 ms per symbol (~44 bits/s).
- The receiver runs an FFT on the mic input every 12 ms, picks the strongest
  tone, and accepts it once it has held for 3 polls.
- "Dial-up" mode moves everything to 1.2–3.4 kHz so you can hear it.

## Run it

The page needs HTTPS for microphone access. Turn on GitHub Pages
(Settings → Pages → deploy from this branch, `/ (root)`) and open the link on
two phones.

## Also in this repo

- [`kuruma/`](kuruma/) — Kuruma-chan, a car that reacts to your driving in Japanese.
