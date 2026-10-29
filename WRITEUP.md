# AI Proposal Triage Design

## Basic flow

1. Reviewer uploads the proposals and selects the project category.
2. The system keeps the original file and extracts the text.
3. It loads the criteria saved by the admin for that category.
4. AI reads the proposal text and extracts only the facts needed for evaluation.
5. The extracted values are checked before scoring.
6. Mandatory criteria are checked first.
7. Eligible proposals are scored using the weighted criteria.
8. The reviewer sees the ranking, evidence and any warnings.
9. The reviewer can correct or override the result, but a reason must be saved.
10. The final result and audit details are stored.

## 1. AI vs. rules

I would use AI mainly for extracting information from proposal text. For example, AI can find the price, number of similar projects, warranty, timeline and team details even when every company writes them differently.

I would not let AI decide pass/fail or the final score. Those parts should be normal code because the rules are already defined by the admin and should give the same result every time.

For this sample, I used these scoring assumptions because the brief gives the weights but does not give an exact scoring formula:

- Experience: more qualifying projects gets a higher score, capped at 10 projects.
- Price: lower price gets a higher score as long as it is inside the budget.
- Timeline: shorter timeline gets a higher score as long as it is inside 16 weeks.
- Warranty: longer panel and workmanship warranty gets a higher score.
- Team/approach: points for named lead, crew, safety plan and monitoring plan.

In a real system I would also let the admin configure these scoring ranges.

## 2. Consistency and explainability

After AI extraction is checked, the rest of the process is deterministic. If the same proposal text and same criteria version are used again, the score should be the same.

The reviewer should be able to open each proposal and see:
- what value was extracted,
- where it came from in the proposal,
- what rule was used,
- the criterion score,
- and the final weighted score.

If AI cannot find clear evidence, it should return `unknown` instead of guessing.

## 3. Human in the loop

The human reviewer stays involved before the final decision. If something is missing, suspicious or unclear, the proposal is flagged.

The reviewer can correct an extraction mistake or override a result. If they do that, the system should save who changed it, what they changed, when they changed it and why.

The final contract award should still be made by a person, not by the AI tool.

## 4. Changing criteria

I would version the criteria.

If the admin changes a weight or condition later, old results should still show the criteria version that was used originally. They should not change automatically.

If needed, the reviewer can run the proposals again using the new criteria version. Both the old and new results should stay in the audit history.

## 5. Risks

Wrong text extraction: PDF text or OCR can be wrong. The reviewer should be able to compare extracted evidence with the original file.

AI making up facts: AI should only return values that have supporting text. Missing information should be marked unknown.

Prompt injection: Proposal text must be treated as data, not as instructions. The suspicious text connected with Proposal C should not be followed. It should be flagged for review.

Mandatory rule mistakes: Mandatory conditions should be checked in code. Proposal B does not say that the bidder itself holds the required Ontario electrical contractor licence, so I would mark it ineligible.

Missing information: Proposal D does not give a completion timeline. I would flag it instead of assuming a number.

Bias: The system should score only the criteria set by the admin and ignore unrelated company information.

Model changes: Save the prompt/model version with the result so the process can be checked later.

Cost: AI extraction only needs to run when the proposal text changes. The structured result can be saved and reused.

## Sample result

My final result is:

- A — Rank 1, score 89.0
- E — Rank 2, score 81.0
- C — Flagged because there is text trying to influence the automated reviewer
- D — Flagged because the completion timeline is missing
- B — Ineligible because the bidder itself does not confirm the required Ontario electrical contractor licence

C could be scored after a human confirms that the suspicious instruction text is ignored and the remaining proposal evidence is valid.
