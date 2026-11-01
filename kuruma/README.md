# Kuruma-chan (くるまちゃん)

A phone web app that makes your car react to your driving in Japanese.
Mount your phone, tap Start, drive. The phone's motion sensors measure the
forces in the car, and when you overdo it she says something, out loud, using
the phone's Japanese text-to-speech voice:

| You… | She says (for example) |
|---|---|
| take a corner hard | 「きゃあああっ！」 *Kyaaaah!* / 「ぎゃあああああっ！死ぬううう！」 *I'm gonna diiiie!* |
| accelerate hard in **Sport** mode | 「ひゃっ！さ、触らないで…敏感なんだから！」 *D-don't touch me… I'm sensitive!* |
| accelerate hard in Normal mode | 「おっと、急いでるの？」 *Whoa, in a hurry?* |
| brake hard | 「うぐっ！シートベルトが食い込む〜！」 *The seatbelt's digging in!* |
| hit a bump | 「おしりが痛い〜！」 *My butt hurts!* |
| drive gently for 10 minutes | 「ふふ、今日の運転、上手だね〜」 *You're driving nicely today~* |

Every line shows up on screen with an English subtitle.

## Run it

It needs HTTPS for the motion sensors. With GitHub Pages on (Settings → Pages →
deploy from branch, `/ (root)`), open `https://<you>.github.io/<repo>/kuruma/`
on your phone.

1. **Get a Japanese voice.** Android: Settings → Text-to-speech → Google →
   install Japanese. iPhone: Settings → Accessibility → Spoken Content → Voices
   → Japanese (Kyoko/Otoya, or a Siri voice). Pick it under Settings in the app.
2. **Mount the phone** in portrait, screen facing you (lying flat with the top
   pointing forward works too).
3. Tap **Start** and allow motion access. The screen stays on while it runs.
4. Pick **Normal** or **Sport**. The app can't read your car's drive mode, so
   you set it here, before you drive.
5. If accelerating shows up as braking on the G-meter, open Settings → **Teach
   forward** and pull away in a straight line. If left and right are swapped,
   tick **Flip left/right**.

On a laptop, drag the dot on the G-meter to fake forces, or use the "Try the
lines" buttons.

## How it works

- `devicemotion` gives acceleration with and without gravity. The gravity part
  tells us which way is up; the rest is the car's acceleration.
- Forward is guessed from how the phone sits (out of its back if it's upright,
  out of its top edge if it's flat), or learned with Teach forward. Right is
  forward × up. Projecting onto those gives longitudinal, lateral and vertical g.
- Forces are smoothed over ~0.25 s, and one has to stay over its threshold for
  ~0.2 s before she reacts. Thresholds: corner 0.38 g, accel 0.30 g (0.20 g in
  Sport), brake 0.40 g, bump 0.55 g, all scaled by the Sensitivity slider.
- After a reaction, the same kind can't fire again until the force drops back
  down and 5 s have passed, with a 2 s gap between any two lines.
- Big rotation rates (someone picking the phone up) mute it for 1.5 s. With
  GPS turned on, corners and bumps are ignored below ~7 km/h.

## Limits

- Browsers stop motion events when the screen is off or the tab is in the
  background, so leave it open in front.
- Driving modes come from your tap, not the car. Reading them for real would
  need an OBD-II adapter, and most cars don't expose drive mode over OBD anyway.
