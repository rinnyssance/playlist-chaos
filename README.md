# 🎵 Playlist Chaos

**Playlist Chaos** is my CodePath AI Engineering project focused on debugging, testing, and refactoring an existing Python application with the help of AI.

Rather than building an application from scratch, this assignment challenged me to work with an existing codebase, reproduce bugs, trace unexpected behavior back to the source code, use AI as a debugging assistant, test proposed solutions, and make focused changes without breaking existing functionality.

The application is built with **Python** and **Streamlit** and organizes songs into different playlists based on their genre, energy level, and a user profile.

---

## 📚 About the Assignment

This project was completed as part of **CodePath's AI Engineering coursework**.

The main goal of the assignment was not simply to ask an AI assistant to fix the application. Instead, I practiced treating AI-generated explanations and solutions as **hypotheses that still needed to be understood and tested**.

My workflow throughout the assignment was:

1. Reproduce the unexpected behavior.
2. Identify the function responsible for it.
3. Ask AI to help explain the code and possible cause.
4. Evaluate the explanation myself.
5. Make the smallest reasonable change.
6. Run the application again.
7. Verify the expected behavior.
8. Commit fixes separately from refactoring changes.

This helped me practice using AI as a development tool while still remaining responsible for understanding and verifying the code.

---

## 🛠️ Built With

- Python
- Streamlit
- Git
- GitHub
- Visual Studio Code
- GitHub Copilot / AI-assisted debugging

---

## 🎧 What Playlist Chaos Does

Playlist Chaos takes song data and organizes songs into mood-based playlists.

Songs can be categorized as:

- **Hype**
- **Chill**
- **Mixed**

The application also includes features for:

- Adding songs
- Searching playlists by artist
- Viewing playlist statistics
- Calculating average energy
- Calculating the Hype Ratio
- Finding the most common artist
- Selecting a random song with Lucky Pick
- Tracking playlist history

---

# 🐛 Part 1 — Debugging Search

The first bug involved searching the **Hype playlist by artist**.

Searching for:

```text
AC
```

was expected to find:

```text
AC/DC
```

but the application returned no matching songs.

## Finding the Problem

I traced the behavior to the `search_songs()` function in `playlist_logic.py`.

The original comparison was effectively checking:

```python
value in q
```

For an artist such as `AC/DC` and a search query of `AC`, this meant the program was checking whether:

```text
"ac/dc" is inside "ac"
```

which is false.

The intended behavior was the opposite: determine whether the user's shorter search query appears inside the artist name.

## My Fix

I changed the comparison to:

```python
if value and q in value:
    filtered.append(song)
```

Now the program checks whether:

```text
"ac" is inside "ac/dc"
```

which correctly returns a match.

I then saved the application, reran it, and verified that partial, case-insensitive artist searches worked correctly.

---

# 📊 Part 2 — Fixing Playlist Statistics

For the second debugging challenge, I chose the **playlist statistics** behavior.

When I inspected the application's statistics, I noticed that the **Hype Ratio** did not make mathematical sense.

For example, the application displayed:

```text
Total Songs: 38
Hype: 12
Hype Ratio: 1.00
```

If only 12 of 38 songs are Hype songs, the ratio cannot be `1.00`.

That told me there was a problem in `compute_playlist_stats()`.

## Hype Ratio Bug

The original calculation used the number of Hype songs as the total:

```python
total = len(hype)
hype_ratio = len(hype) / total if total > 0 else 0.0
```

That essentially calculates:

```text
Hype Songs / Hype Songs
```

which produces `1.00` whenever at least one Hype song exists.

I changed the total to include **all songs**:

```python
total = len(all_songs)
hype_ratio = len(hype) / total if total > 0 else 0.0
```

After testing the fix, an example playlist containing:

```text
22 total songs
11 Hype songs
```

correctly produced:

```text
Hype Ratio: 0.50
```

---

## Average Energy Bug

While working with the statistics function, I also found a related problem with the **Average Energy** calculation.

The original code summed the energy values from only the Hype playlist:

```python
total_energy = sum(song.get("energy", 0) for song in hype)
```

but divided that result by the number of songs across all playlists.

Those two groups did not match.

I changed the calculation to use every song:

```python
total_energy = sum(song.get("energy", 0) for song in all_songs)
avg_energy = total_energy / len(all_songs)
```

Now both parts of the calculation use the same collection of songs.

After restarting and testing the Streamlit application, the statistics updated correctly.

---

# ♻️ Part 3 — Refactoring

After fixing the application's behavior, I practiced **refactoring working code without intentionally changing what it does**.

I chose the `most_common_artist()` function.

Originally, the function sorted every artist by count and then returned the first result:

```python
items = sorted(
    counts.items(),
    key=lambda item: item[1],
    reverse=True
)

return items[0]
```

The goal, however, was simply to find the artist with the highest count.

I refactored this to:

```python
return max(counts.items(), key=lambda item: item[1])
```

This communicates the intention more directly and avoids sorting the entire collection just to retrieve its largest value.

I then reran the application and verified that the **Most Common Artist** feature continued to behave correctly.

---

# 🧪 Testing My Changes

I tested changes through the running Streamlit application rather than assuming that a code change was correct.

My checks included:

| Test | Expected Result |
| --- | --- |
| Search `AC` | Finds `AC/DC` |
| Search with different capitalization | Search remains case-insensitive |
| View Hype Ratio | Uses Hype songs divided by all songs |
| 11 Hype / 22 Total | Displays `0.50` |
| View Average Energy | Uses energy from all songs |
| Refactor `most_common_artist()` | Produces the same application behavior |

One thing I encountered during testing was **stale application state**. At one point, the code had been changed correctly but the Streamlit interface was still showing the previous result. Saving the file and restarting/rerunning the application helped me distinguish between an incorrect fix and an application that had not reloaded the latest code.

---

# 🤖 Working With AI

An important part of this assignment was learning that using AI for programming is not the same as automatically accepting its output.

I used AI to help:

- Explain unfamiliar code
- Trace program logic
- Identify possible causes of bugs
- Compare expected and actual behavior
- Discuss focused fixes
- Understand refactoring opportunities

However, I still needed to reproduce each problem and test whether the proposed change actually solved it.

The biggest lesson I took from this project is:

> **AI can suggest what might be wrong, but running and testing the code is what tells me whether the suggestion is actually correct.**

That became especially clear when some of the suggested bugs did not reproduce in my version of the application. Instead of changing code simply because a bug was suggested, I tested the behavior first and chose a problem I could actually observe.

---

# 💻 Running the Project Locally

Clone the repository:

```bash
git clone https://github.com/rinnyssance/playlist-chaos.git
```

Move into the project:

```bash
cd playlist-chaos
```

Create a virtual environment:

```bash
python -m venv .venv
```

### Windows PowerShell

Activate the environment:

```powershell
.\.venv\Scripts\Activate.ps1
```

Install the project dependencies:

```bash
pip install -r requirements.txt
```

Start the Streamlit application:

```bash
streamlit run app.py
```

Streamlit will provide a local URL that can be opened in a browser.

---

# 🗂️ Repository Hygiene

My local Python environment and generated Python cache files are excluded from Git using `.gitignore`:

```gitignore
.venv/
__pycache__/
*.pyc
```

This keeps machine-specific and automatically generated files out of the repository.

---

# 🌱 What I Learned

This project gave me hands-on practice with more than just fixing Python syntax.

I practiced:

- Navigating an unfamiliar codebase
- Reproducing bugs before changing code
- Reading existing Python functions
- Following data through an application
- Debugging logical errors
- Testing expected versus actual behavior
- Using AI suggestions critically
- Making focused code changes
- Refactoring without changing behavior
- Working with Streamlit
- Managing Python virtual environments
- Using `.gitignore`
- Creating meaningful Git commits
- Separating bug fixes from refactoring
- Creating and pushing a repository with Git and GitHub

Most importantly, I learned to approach AI-assisted development as a process of **investigation and verification**, rather than simply asking AI to generate an answer.

---

## 👩🏾‍💻 Author

**Erin Joel Moore**

Creative Technologist | Scientist | AI Engineering Student

- GitHub: [@rinnyssance](https://github.com/rinnyssance)
- LinkedIn: [ejmoore789](https://www.linkedin.com/in/ejmoore789/)

---

## 🎓 Course

**CodePath — AI Engineering**

Playlist Chaos  
Module 1 Tinker Assignment
