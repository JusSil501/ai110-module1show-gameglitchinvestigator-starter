# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

- [x] **Game purpose:** A number guessing game where the player tries to guess a secret number within a limited number of attempts. The game gives directional hints ("Go Higher / Go Lower") after each guess and tracks a running score. Difficulty controls the number range and attempt limit.

- [x] **Bugs found:**
  1. **Inverted hint messages** — `check_guess` returned "📈 Go HIGHER!" when the guess was too high and "📉 Go LOWER!" when it was too low. The messages were completely swapped, making it impossible to converge on the secret number by following the hints.
  2. **Secret silently cast to string on even attempts** — On every even-numbered attempt, `app.py` converted the secret to a `str` before passing it to `check_guess`. This triggered the `TypeError` fallback path which used *lexicographic* (alphabetical) comparison instead of numeric. For example, `"9" > "50"` is `True` alphabetically, causing wrong outcomes on roughly half of all guesses.
  3. **New Game button didn't reset game status** — After winning or losing, clicking "New Game" set a new secret but never reset `st.session_state.status`. Because `status` remained `"won"` or `"lost"`, the app immediately called `st.stop()` on the next render, making the game unplayable without a full page refresh.

- [x] **Fixes applied:**
  1. **Inverted hints** — Swapped the return strings in `check_guess` so `guess > secret` → `"📉 Go LOWER!"` and `guess < secret` → `"📈 Go HIGHER!"`. Added corresponding fixes in both the main branch and the `TypeError` fallback.
  2. **New Game status reset** — Added `st.session_state.status = "playing"` and `st.session_state.history = []` to the `new_game` handler so the game is fully reset on every new game. Also corrected `random.randint(1, 100)` to use the actual difficulty range (`low, high`).
  3. **Logic refactored into `logic_utils.py`** — Moved `get_range_for_difficulty`, `parse_guess`, `check_guess`, and `update_score` out of `app.py` and into `logic_utils.py` so that `tests/test_game_logic.py` can import and run them. Updated `app.py` to import from `logic_utils`.

## 📸 Demo

![Fixed winning game](Screenshot%202026-03-03%20at%206.44.11%20PM.png)

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, insert a screenshot of your Enhanced Game UI here]
