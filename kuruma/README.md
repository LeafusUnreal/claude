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

## Install it

Send your mates the link: `https://<you>.github.io/<repo>/kuruma/`
(GitHub Pages needs to be on: Settings → Pages → deploy from a branch, `/ (root)`).

The first time it opens, a setup screen walks them through it for Samsung,
other Android phones or iPhone. It reopens from the **?** button.

1. **Install it.** Android Chrome: tap **Install Kuruma-chan** (or ⋮ → Add to
   home screen). iPhone Safari: Share → **Add to Home Screen**. It gets its own
   icon, opens full screen, and works offline once it has been opened once.
2. **Get a Japanese voice.** The app speaks with the phone's own text-to-speech.
   - Samsung: Settings → General management → Text-to-speech output → Preferred
     engine **Speech Services by Google** → ⚙️ → Install voice data → Japanese.
   - Other Android: Settings → System → Languages → Text-to-speech output →
     same as above.
   - iPhone: Settings → Accessibility → Spoken Content → Voices → Japanese.

   Then tap **Test voice** on the setup screen.
3. **Drive.** Mount the phone upright, screen facing you. Pick Normal or Sport
   (the app can't read your car's real mode), tap Start, and keep the app on
   screen.

If accelerating shows up as braking on the G-meter, use Settings → **Teach
forward** and pull away in a straight line. If left and right are swapped, tick
**Flip left/right**.

Updates reach everyone automatically: the app checks for a new version each
time it opens with signal.

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
