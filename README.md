# Tasmia Chowdhury - Campus Life

<!-- Replace this line with your name and which corpus you picked. 

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.
-->

# Unit 1

## What This Does

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

     This is a RAG project built for an imaginary campus. The RAG answers questions that are specific to this imaginary school based only off the documents 
     in campus_life corpus. Every answer is grounded in real documents if the LLM gets a question that is completely irrelevant to campus/uni life, it refuses to answer. 
     If there is a question that is related to campus life but the RAG pipeline cannot confidently answer the question it says so instead of hallucinating information that is not strictly in the corpus. (well thats the goal... we'll see if the RAG hallucinates)

## Chunking Strategy

**Chunk size:** 1 document per chunk
**Overlap:** 0 

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->
     I chose to keep each document in tact instead of cutting it off by a character window, so I didn't get a split arbiturary sentence, with no context. The only time I split is when a document exceeds 800 characters, and then it defaults and breaks along the paragraphs.

     Overlap: Set to 0. Because each post is self-contained and kept whole, there are no adjacent slices of the same thought that require overlapping text to preserve context.


## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->


**Chunk 1** — admin_add_drop_deadline.txt#0  `` — produced by:chunker.py::split_documents  ``

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: course_biol_160.txt#0 `` — produced by:chunker.py::split_documents ``

```
BIOL 160 Cell Biology

I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: course_hist_118_workload.txt#0  `` — produced by:chunker.py::split_documents ``

```
Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** — source: dining_pellew_dining_hall_followup.txt#0 `` — produced by:chunker.py::split_documents ``

```
Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.
```

**Chunk 5** — source: housing_innisfree_hall.txt#0 `` — produced by:chunker.py::split_documents ``

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

**Question:**
     What do students say the average wait times is at Halden Hall?
**Answer:**

```
(best distance 0.2475, cutoff 0.6)

Students say the wait time at Halden Hall is rarely more than 8 minutes, even at noon.

Sources: `dining_halden_hall.txt` and `dining_halden_hall_followup.txt`

Sources retrieved: dining_halden_hall.txt, dining_halden_hall_followup.txt, dining_kestrel_commons_followup.txt, dining_pellew_dining_hall_followup.txt, dining_the_ridgeway_cafe_followup.txt

1 model calls this session, 738 tokens (689 in, 49 out)
```

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->
**My relevance cutoff:** `0.60`

To set the cutoff, I measured the best retrieval distance across five in-scope questions and five out-of-scope questions:
* **In-scope distances:** Ranged from `0.2475` to `0.3996` (mean ~0.298).
* **Out-of-scope distances:** Ranged from `0.825` to `0.934` (mean ~0.877).

There is a clean, distinct gap of over 0.42 between my highest in-corpus distance (`0.3996` for BIOL 160) and my lowest out-of-scope distance (`0.825` for the capital of Mongolia). I retained the default threshold of `0.60` in `config.py` because it sits comfortably in the middle of this gap (~0.40 to ~0.82), safely gating out completely irrelevant queries while ensuring valid campus questions are never rejected.

| Question | In corpus? | Best distance |
|---|---|---|
| What do students say the average wait times is at Halden Hall? | Yes | 0.2475 |
| How many hours outside of class do students say you should expect to put into STAT 150? | Yes |0.2785 |
| When is the earliest I can book a group study room? | Yes |0.2799 |
| Does BIOL 160 have a curve?| Yes | 0.3996|
| How much can I print per semester?| Yes | 0.2875 |
| What is the capital of Mongolia? | No | 0.825 |
| How do I change the oil in a diesel engine? | No | 0.934 |
| Who won the 1994 World Cup? | No | 0.886 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.844 |
| How do I write a for loop in Rust? | No | 0.896 |


## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I used AI to help me decide what my ceiling for a cutoff of a chunk should be. I kept the documents in tact since they were short, ~340 characters and contained opinion based sentences sometimes. But in the case a document had multiple paragraphs, it would split around ~800 characters and only at the start of a paragraph. I specifically asked "After how many characters should I split a document into seperate chunks; and min chars: _ avg_chars: _ max_chars: " It reasoned well and explained that 800 should be the ceiling because that's when you'd have two strong complete thoughts, and splitting prematurely would create an arbituary sentence, which would be a problem since some of the documents contain opinion based sentences that can easily apply to other campus life topics "The one piece of advice: the essay rubric is posted in week 2 and it's followed exactly — read it early." 


**2.** I asked AI to generate the Markdown table and calculate the separation gap between my in-scope and out-of-scope retrieval distances. It provided a table along with a recommendation to aggressively lower `THRESHOLD` to `0.45` to be conservative. However, I rejected lowering it that far because my BIOL 160 question had a distance of `0.3996`—a cutoff of `0.45` left too thin a safety margin for slight variations in student phrasing. Instead, I kept the cutoff at `0.60`, which sits at the true midpoint of the gap and prevents false refusals.

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
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

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
