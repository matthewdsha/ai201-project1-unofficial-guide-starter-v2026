# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
<!-- e.g. "One of my questions is about a topic only two documents mention, so
     I expect that one to be hard." -->

Chunks should contain answers to the questions, but occasionally a document may be pulled related to the topic of the question, but not what it is asking. This is a real risk in campus_life because a few topics span more than one file — e.g. `dining_pellew_dining_hall_followup.txt` is a separate chunk from the original Pellew post it replies to — so the embedding could rank that related file above the one that states the answer. So using 4 of 5 test questions is good to test it pulls the answer a majority of the time.

> **Revised in unit 2:** For at least 4 of 5 questions, the answer can be
> found somewhere across the retrieved chunks as a whole, not necessarily in
> any single one of them.
>
> **Why revised:** "one that contains the answer" reads two ways that
> disagree: strictly, "housing buildings" fails since no single chunk covers
> more than one building, giving 3 of 5 (MISS); loosely, it's 4 of 5 (MET).
> I'm keeping the loose reading because it matches how a user actually
> experiences the system — reading the assembled answer, not one isolated
> chunk.
---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
<!-- Why all five and not four? What about your setup makes that achievable —
     or what would have to go wrong for it not to be? -->

Every answer MUST have a source, and this is because if there is no source, then the answer is just made up. The answer should be from a source in order to prove accuracy. `generate.py`'s `GROUNDING_INSTRUCTION` explicitly tells the model to name the filename of the excerpt it used and to say it doesn't have enough information rather than guess, so citing is baked into every prompt, not left to the model's discretion. There are a lot of documents, so getting an answer from a source is very feasible. The only way it would go wrong is if a user potentially asks a question with an answer that does not exist in a source.
---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
<!-- What did your distances look like when you set the cutoff in Milestone 4?
     Was there a clean gap, or did the two groups overlap? -->

Occasionally a question may be on the edge of relevance with the documents, so this could potentially cause the relevance gate to think there is enough information. My Milestone 4 distances back this up: the two groups didn't overlap, but the gap was closer on the in-corpus side than I'd like — my two hardest in-corpus questions (math exams at 0.534, health center at 0.539) sat only ~0.06 below the 0.6 default, while out-of-scope distances started at 0.825. I set THRESHOLD to 0.68, splitting that gap, but a real out-of-scope question worded more like an in-corpus one could still land under it. Using 4 of 5 tries is a good number to show a majority doesn't return an actual answer when there isn't enough documents to cover. It's okay if information from documents are returned if its related to the question, even if it doesn't cover the answer as long as it doesn't happen often.

---

## 4. The retreived chunks should be the right size

The retrieved chunks should be more than 150 characters and no more than 600 characters each. This should happen in every chunk.

<!-- YOU WRITE THIS ONE.

     How would you know if your chunks were the right size? Name something
     countable or observable.

     Examples of the right shape — don't copy these, they should come from
     what you actually saw in Milestone 3:
       - "At least 4 of 5 sampled chunks read as a complete thought, with no
          sentence cut in half at either end."
       - "No chunk is shorter than 200 characters, since anything below that
          in my corpus turned out to be a heading with no content under it." -->



**Why this target:**

Too much information makes it hard to narrow what is needed for a response while too little makes responses lack support. This is why having between 150 and 600 characters is a good amount. The 600 ceiling matches `CHUNK_SIZE` in config.py, which is also why campus_life comes out as one chunk per post — almost nothing in the corpus reaches that length. The 150 floor comes from my shortest real document, `course_hist_118_exams.txt`, at 183 characters: it's a complete, legitimate answer, and an original floor of 200 would have excluded it as if it were a content-free heading. This should happen in every chunk as we want good chunks that provide relevant information.

---

## 5. The response should contain some key words from the question

When our 5 test questions are asked, 4 of 5 responses should contain at least 2 key words that were in the question.

<!-- YOU WRITE THIS ONE TOO.

     Pick something you actually care about getting right. It could be about
     speed, about refusals, about a particular kind of question your corpus
     handles badly, about source attribution being correct rather than merely
     present — anything, as long as it names a number or an observable
     outcome. -->



**Why this target:**

An answer should be relevant to the question, and we can restate the question key words to show it is related. Having there be at least 2 key words is a good amount, and having this in 4 of 5 responses shows it happens for a majority of the time. `generate.py`'s `GROUNDING_INSTRUCTION` tells the model to "be brief, two or three sentences," which pushes it toward paraphrasing rather than echoing the question's exact wording — e.g. a question about "wait times at Commons" could come back answered purely in terms of minutes and the building name. Occasionally, we may have a question with very few key words.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
