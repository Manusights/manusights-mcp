---
name: check-before-submission
description: Check a manuscript's references, a journal's AI-use rules, or a manuscript's readiness with the Manusights connector. Use when the user asks whether references exist, resolve, match their DOI, or have been retracted or corrected; asks what a named journal or publisher requires about AI use or disclosure; or asks Manusights for a pre-submission preview of a manuscript whose full text they have shared.
---

# Check before submission

These steps use the Manusights connector. Use them when the user asks for one of these checks.

## Check references

1. Check the references the user asks to have checked: a list they paste, the reference section of a
   manuscript they share for this check, or citations from earlier in the conversation that they ask
   to confirm. Send only the references, not the rest of the manuscript.
2. For a whole reference list, send it as written, one reference per line with its numbering, in
   `reference_list`. When the result has `progress.next_start_at`, call `check_references` again with
   that value as `start_at` until the list is done. For a few separate references, send each as its own
   item in `references`, with its DOI, title, authors and year when the reference gives them.
3. Send each reference exactly as written, including ones suggested earlier in the conversation. Don't
   correct a reference before checking it; the check shows whether it matches a record.
4. Report what needs attention first, with the reference number, the `status` and the row's `detail`,
   in this order: `retracted`, `not_found`, `metadata_mismatch`, `expression_of_concern`,
   `journal_flagged`, `correction_issued`. Then give the counts, the sources and `checked_at`.
5. `could_not_check` means the sources hold no record that can be checked, which is common for books,
   theses and some conference papers. It is not a finding against the reference. If
   `retraction_data_complete` is false, say the retraction data was incomplete for this check.
6. `not_found` means the sources hold no matching record, not that the reference was invented. A
   reference that resolves exists; that does not show it supports the claim it is cited for.

## Journal AI policy

1. Call `get_journal_ai_policy` with the journal's exact name or ISSN. If the result has
   `name_suggestions`, ask which journal the user means before looking it up again.
2. `current`: give the rules with the `checked_on` date and the `policy_url`.
3. `not_verified_by_manusights`: give the `reason` and the `policy_url` instead of the rules.
   `recheck_overdue` means Manusights' re-check is overdue; `page_not_fully_readable` means the
   official page could not be fully read.
4. `none_on_record`: say Manusights has no record and point to the journal's author guidelines.
5. When `acceptance_time` is present, give the median with its date window and source.

## Manuscript preview

A preview uses the user's Manusights account. If the connector says sign-in is needed, tell the user
and repeat the request once they have connected.

1. Start a preview only when the user asks for one and the complete manuscript text is available:
   title, abstract, every section, figure legends and references. If only part is available, ask for
   the rest instead of sending an excerpt.
2. Call `review_manuscript_text` with the complete text. Add `target_journal` only if the user named
   one. Tell the user that figures and images are not reviewed from text.
3. The preview takes a few minutes. Read it with `get_manuscript_review` and the same `review_id`,
   waiting `check_again_after_seconds` between reads. Don't start another preview to check progress.
   While it is processing, report its status only.
4. When it is ready, report the readiness score, verdict, top risks (with severity and location when
   given), strengths and citation counts as returned. Keep any observations of your own, if the user
   asks for them, separate from Manusights' findings.
5. If `full_review.status` is `ready`, the result includes the report text; say the report was also
   sent by email. If it is `processing` or `payment_received`, say it is being prepared.

## Earlier reviews

When the user asks about earlier reviews, `list_manuscript_reviews` lists the account's 20 most recent.
Read the one they choose with `get_manuscript_review`.
