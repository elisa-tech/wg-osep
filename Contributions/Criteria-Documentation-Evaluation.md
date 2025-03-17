# Criteria for documentation evaluation

| Desirable Property | Explanation |
| :---- | :---- |
| Clear | Documents must be unambiguously clear to the intended readers (not to anyone, just to the people active on the project) |
| Comprehensible | A document is comprehensible if the stakeholders and consumers of the document understand its meaning |
| Detail | Documents must detail complex concepts as a set of elementary measurable concepts |
| Scale | Documents must specify a scale of measure to define the concept  |
| Quantity | Given a Scale, documents must specify at least two points of reference on the defined ‘Scale’ to define relative terms (e.g. ‘higher’.) These can be called ‘benchmark’ and ‘target’ in the specification.  |
| Qualify | ‘Target’ must specify ‘when’ a performance level is available. Other qualifiers such as ‘when’ and ‘if’ should be explicit if not elsewhere specified. |
| Complete | A complete document is clear without further elaboration because it sufficiently describes the capability and characteristics it is intended to cover. |
| Correct | A document is correct when it is either free from error or it is accurate. |
| Consistent | A document is consistent if it contains no contradictions. |
| Verifiable | A document is verifiable if it is possible to demonstrate that all assertions in the document can be reproduced. There are no hypotheses in the document. The assertions can be verified by either demonstration, observation or review. |
| Reproducible | Reproducibility is specifically about what a document is referencing.  When a document describes a project sufficiently that it can be recreated, the document is considered reproducible. |
| Compliant | A document is compliant when it is being used to satisfy a standard.  The standard can be from a public source, such as IEC or ISO, or it can be a regulation as from a government, such as FMVSS.  |
| Maintainable | A document is maintainable when it can be modified or extended, e.g. by the introduction of new versions or by adding/removing sections. |
| Grammatical | A document is grammatical when it is well formed.  The document is in accordance with the productive rules of the grammar of a language.  Because there is variation in what is expected, the grammatical rules followed for a given document should be specified before the document is created. For example, a requirements document could be specified to conform to the syntactic rules of EARS (Easy Approach to Requirement Syntax.) |

### File metadata

| Metadata | Explanation |
| :---- | :---- |
| Major Version | X(.0)  |
| Minor Version | Example: X(.1 to .6) – Minor version can be an indicator of the maturity of the version |
|| X.1 – Initial version 🡪 Send for peer review |
|| X.2 – Peer review feedback received 🡪 Mark as X.2, incorporate feedback |
|| X.3 – Feedback incorporated 🡪 Mark as X.3, send for expert review |
|| X.4 – Expert feedback received 🡪 Mark as X.4, incorporate feedback |
|| X.5 – Feedback incorporated 🡪 Mark as X.5, send for acceptance review |
|| X.6 – Acceptance review received 🡪 Mark as X.6, incorporate feedback |
|| (X+1).0 – Feedback incorporated 🡪 Mark as next Major version (.0), release |
| ID | GUID? |
| Status | (See below) |

### Status (Example only)

| Status |
| :---- |
| Proposed |
| In Progress |
| Under Review |
| Verified |
| Released |

### File header metadata (Example only)

| Metadata | Explanation |
| :---- | :---- |
| Title | Title of the document |
| Author | Author of the document. Can be multiple authors. |
| Lead Reviewer | Person responsible for reviewing, gathering feedback and providing feedback to the author. |
| Reviewers | Reviewer of the document, should have relevant experience. |
| Date | Date of the current version of the document. Note that this can differ from the file date. |

### Review Ratings (Example only)

| Rating | Explanation |
| :---- | :---- |
| Critical  | Showstopper, the document is fundamentally incorrect in its current state and should be reworked.  Must be fixed immediately. |
| Severe | Major problem with the document.  Must be addressed or fixed before the document can be considered for further review. |
| Minor | Problem with the document, should be corrected, but review can continue. |
| Observation | Comment from the reviewer to the author about how something might be improved.  Not a problem with the document. |
| Question | Could be considered as a minor also.  This is a question from the reviewer to the author to elaborate or clarify something that is unclear.  Could also be considered as a violation of the Clear, Comprehensible, or Detail property. |

## Document review process:

Entry Conditions:

* An enumeration method (e.g. headings, line numbers, etc.) should be available for ease of review document feedback references.
* A mechanism should be available for tracking changes to a review document.
* Author requests the review
  * Author can use the review criteria to determine if the document is mature enough to be reviewed
  * The Author fills out the file metadata
  * The Author fills out the file header metadata
* Review lead is selected from the approved list
* Reviewers are selected by the review lead
* Relevant documents are made available
  * Perform review
    * Violations should be noted as: Critical (C), Severe (S), Minor (M), Observation (O), Question (Q)
  * Mark feedback according to criteria
  * Feedback should be included as comments unless the change is minor
  * Feedback should be assigned to the author to address
  * Note to author:
    * The reviewers are not directing their feedback at you or your ability.  The purpose of this is to find potential bugs and minimize systematic error in the documentation

Exit Condition:

* Fewer remaining major defects per page than the agreed exit standard
* Recommendation is one (1) Severe (or higher) defect per page as an initial exit criterion

## Review Checklist

| Review ID | Criterion | Reference | Rating | Comments |
| :---- | :---- | :---- | :---- | :---- |
| ID\_01 | Is the document unambiguously clear to the target audience? | Location(s) of the violation of the criterion | C, S, M, O, Q  | Feedback on what the problem is and a suggested correction. |
| ID\_02 | Do the stakeholders and consumers of the document understand its meaning? |  |  |  |
| ID\_03 | Is there sufficient detail? |  |  |  |
| ID\_04 | Are complex concepts described as measurable elementary concepts? |  |  |  |
| ID\_05 | Is there a scale defined for any measurable concepts? |  |  |  |
| ID\_06 | … |  |  |  |
