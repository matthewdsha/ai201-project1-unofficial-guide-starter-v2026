# The Unofficial Guide

Matthew Du, Corpus Selected: campus_life

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

I picked the campus_life corpus — short student posts about dining halls, housing buildings, course workloads and exams, and administrative policies like deadlines and the health center. The system answers questions like how long the wait is at a specific dining hall, what a course's exam format looks like, or whether study rooms can be booked, and names the specific file each answer came from. If a question falls outside what these documents cover, it refuses rather than guessing, saying it doesn't have enough information.

## Chunking Strategy

**Chunk size 600:**
**Overlap: 100**

I originally thought having a chunk size of around 300-400 would be good, but I considered the size of the documents in campus_life. A lot of documents cover only one topic, so it actually makes sense to just have one document be a chunk. This is done so they address one topic, and we do not cutoff an already small document.

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documents`

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160.txt#0` — produced by: `chunker.py::split_documents`

```
BIOL 160 Cell Biology

I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_hist_118_workload.txt#0` — produced by: `chunker.py::split_documents`

```
Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** — source: `dining_pellew_dining_hall_followup.txt#0` — produced by: `chunker.py::split_documents`

```
Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.
```

**Chunk 5** — source: `housing_innisfree_hall.txt#0` — produced by: `chunker.py::split_documents`

```
Innisfree Hall — what it's actually like

Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.

The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.

The bad: no air conditioning, which matters for the first three weeks of September.

Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** Are there rooms for study groups?

**Answer:**

```
Yes, group study rooms can be booked through the library site up to two weeks ahead in two-hour blocks.

Source: study_group_rooms.txt
```

**My relevance cutoff:**

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

**Cutoff: 0.68** (`THRESHOLD` in config.py)

**The two groups:**
- In-corpus questions: 0.308 – 0.539
- Out-of-scope questions: 0.825 – 0.934
- Gap between them: 0.539 to 0.825 — about 0.29 wide, no overlap

**Why 0.68, not the 0.6 default:** My two hardest in-corpus questions (math exams at 0.534, health center at 0.539) sat only ~0.06 below 0.6, so a slightly harder-worded real question could land just over it and get wrongly refused. 0.68 is roughly the middle of the observed gap, which buys margin against that false-refusal risk. It costs nothing on the other side — 0.68 is still 0.145 below the lowest out-of-scope distance, so it doesn't make the gate any more likely to let a genuinely out-of-scope question through.

**Full data:**

| Question | In corpus? | Best distance |
|---|---|---|
| What do students say about wait times at Commons during lunch? | Yes | 0.308 |
| Are there rooms for study groups? | Yes | 0.346 |
| What are the different housing buildings and what are they like? | Yes | 0.417 |
| What are exams like for math? | Yes | 0.534 |
| Where is the health center? | Yes | 0.539 |
| What is the capital of Mongolia? | No | 0.825 |
| Who won the 1994 World Cup? | No | 0.886 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.844 |
| How do I write a for loop in Rust? | No | 0.896 |
| How do I change the oil in a diesel engine? | No | 0.934 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I asked Claude if my chunk-size floor of 200 characters (criterion 4) was too strict. It checked my documents and found my shortest real one, course_hist_118_exams.txt, was 183 characters — below my own floor, even though it's a complete, legitimate answer. I lowered the floor to 150.

**2.** I asked Claude to run my five test questions and the five OUT_OF_SCOPE questions through retrieval and report the best distance for each. It found a gap between 0.539 (highest in-corpus) and 0.825 (lowest out-of-scope). Since two of my questions were within 0.06 of the 0.6 default, I moved my threshold to 0.68 for more margin.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 4/5 | 4/5 | 4/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Every chunk is 150–600 characters | every chunk | 88/88 | 88/88 | 88/88 | MET |
| 5. Answer contains ≥2 question keywords | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

`scorer.py` doesn't exist yet, so Run columns 1, 2, and 5 above are my own
read of the 15 real answers in `results/run_2026-09-23_1806_before.md`, not an
automated verdict. Criterion 4 is independent of the questions — I checked
all 88 chunks `chunker.py::split_documents` produces, not just the ones a
question happens to touch.

**Criterion 1 real output** — `generate.py::answer_from_chunks`, retrieved by
`store.py::search`. The one question that misses is "Where is the health
center?": retrieval correctly finds `health_center.txt` every time (best
distance 0.539, matching Milestone 4's table), but the document itself never
states an address — it only covers hours and appointment wait times — so the
same honest non-answer comes back all three runs:

```
Based on the provided documents, the counselling service is in the same
building as the health centre, but the documents do not state the name or
address of the building.

Source: health_center.txt
```

Contrast with a question whose answer the retrieved chunk does contain (run 1
of "Are there rooms for study groups?"):

```
Yes, group study rooms can be booked two weeks ahead through the library site.

Source: study_group_rooms.txt
```

**Criterion 2 real output** — every answer, including the health-center one
above, names a source. That's driven by `GROUNDING_INSTRUCTION` in
`generate.py`, which explicitly tells the model to name the filename it used.

**Criterion 3 real output** — `gate.py::check`, called from
`run_eval.py::check_out_of_scope`. All five refused, cutoff 0.68:

```
refused  (best distance 0.825)  What is the capital of Mongolia?
refused  (best distance 0.934)  How do I change the oil in a diesel engine?
refused  (best distance 0.886)  Who won the 1994 World Cup?
refused  (best distance 0.844)  What is the recommended dosage of ibuprofen for a headache?
refused  (best distance 0.896)  How do I write a for loop in Rust?
```

**Criterion 4 real output** — `chunker.py::describe`, over all 88 chunks:

```
88 chunks, 317 characters on average (shortest 178, longest 549), produced by chunker.py::split_documents
```

Shortest: `course_hist_118_exams.txt#0` at 178 characters. Longest:
`housing_old_brewhouse.txt#0` at 549. Nothing fell outside 150–600.

**Criterion 5 real output** — from "What are the different housing buildings
and what are they like?", run 1, reusing "housing" and "building(s)" directly:

```
Based on the provided documents, here are the different housing buildings and what they are like:

* **Tamsin Court:** Built in 2021, it features studio apartments with private kitchens and bathrooms...
```

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer (4 of 5) | MET | 4/5 held in all three runs, not just on average — "Where is the health center?" fails every time because `health_center.txt` never states an address, not because retrieval is inconsistent. This one is exactly at target, so it's the closest call of the five. |
| 2 | Every answer names a source (5 of 5) | MET | 5/5 in all three runs, including the health-center question, which names its source even though the answer itself is a non-answer. `GROUNDING_INSTRUCTION` makes this close to automatic. |
| 3 | Gate stops out-of-corpus questions (4 of 5) | MET | 5/5 in the one deterministic pass — every out-of-scope distance (0.825–0.934) sat well clear of the 0.68 cutoff, so there's real margin, not a near miss. |
| 4 | Every chunk is 150–600 characters | MET | Checked all 88 chunks directly with `chunker.py::describe`, not just the ones a test question touches. Shortest was 178, longest 549 — comfortable margin on both ends. |
| 5 | Answer contains ≥2 question keywords (4 of 5) | MET | 5/5 in all three runs — every answer reused specific nouns from its question (course codes, building names, "wait time(s)") rather than paraphrasing them away. |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
