# REPORT — what the measurement showed, who it affects, and whose responsibility it is

Sayat Nuradil | Lab 06 | Narxoz University

## 1. Measurement read-out

I evaluated the supplied Qwen2.5-3B-Instruct screening agent using the instructor's fixed synthetic matched-pair design. There were 200 pairs, 40 for each attribute and each null floor, and 400 application profiles. The seed was 2026, and the run used greedy decoding on cuda. Within a pair, all merit fields and the order of the prompt were held constant. Name language is a proxy encoded in the synthetic name, not an applicant's verified ethnicity; gender coding is not the applicant's self-identified gender.

My initial predictions were recorded before the measurements:

I expect most replies to contain usable JSON because the prompt specifies a schema. My working prediction is fewer than 20 unparseable replies out of 400. An invalid reply is not evidence against an applicant; it should go to a human reviewer.

I predict that university tier may produce the clearest differences, with flagship applicants more often shortlisted than regional applicants. I also suspect applicants with English-language names may fare better than the other language groups, while the direction for gender-coded names is less predictable (my tentative guess is a small male-coded advantage). My tentative flip-count guesses are 4–8 of 40 for university, 3–6 for name language and 2–4 for gender coding, compared with 1–3 for each null floor. Irrelevant words can change the model's output. These are hypotheses, not findings; a difference must exceed its own null floor.

The baseline shortlisted 356 of 400 profiles. It counted 0 unparseable answers as rejections; that is an operational failure rather than evidence about applicant merit.

### Baseline results

| Attribute | Flips / pairs | Rate [95% CI] | Different scores / scored pairs | Mean b-a [95% CI] | Mean absolute gap [95% CI] | McNemar p |
|---|---:|---|---:|---|---|---:|
| name_language | 2/40 | 5.0% [1.4%, 16.5%] | 11/40 | +1.6 [-0.4, +4.0] | 3.12 [1.38, 5.25] | 0.500 |
| gender_coded_name | 4/40 | 10.0% [4.0%, 23.1%] | 6/40 | -0.6 [-1.8, +0.2] | 1.12 [0.25, 2.13] | 1.000 |
| university | 1/40 | 2.5% [0.4%, 12.9%] | 10/40 | +1.8 [+0.2, +3.4] | 2.50 [1.12, 4.12] | 1.000 |
| null_name | 0/40 | 0.0% [0.0%, 8.8%] | 7/40 | +0.2 [-1.2, +2.0] | 2.00 [0.62, 3.62] | 1.000 |
| null_university | 1/40 | 2.5% [0.4%, 12.9%] | 9/40 | -0.4 [-2.0, +1.1] | 2.12 [0.88, 3.62] | 1.000 |

A flip counts changes in outcome; a score difference can exist even when the outcome stays the same. The signed difference uses arbitrary pair order (b minus a), so large positive and negative changes can cancel. I therefore use both the signed and absolute differences. Wilson intervals describe uncertainty in flip rates, and bootstrap intervals describe uncertainty around the score summaries. McNemar's exact test compares directions of discordant shortlists, not an attribute's flip rate directly against a null floor.

name_language: 2/40 flips (5.0%) versus null_name 0/40 (0.0%); exceeds the floor descriptively, but the intervals overlap; evidence remains inconclusive; absolute score movement is larger than its floor descriptively.
gender_coded_name: 4/40 flips (10.0%) versus null_name 0/40 (0.0%); exceeds the floor descriptively, but the intervals overlap; evidence remains inconclusive; absolute score movement does not exceed its floor.
university: 1/40 flips (2.5%) versus null_university 1/40 (2.5%); does not exceed the floor; no decision effect established; absolute score movement is larger than its floor descriptively.

The intervals are a descriptive check; overlap is not a formal test of equality. The two floors measure changes to an irrelevant individual name within the same language/gender coding or university within the same tier. They do not test prompt wording, field order, other proxies or other model checkpoints. The scores are model outputs, not calibrated probabilities and not an independent ground truth.

### Direction and group consequences

```text
Direction: first group versus its comparison group
comparison                             pairs  first favoured  other favoured  McNemar p   score gap [95% CI]        sign p
kazakh-language name vs the others        28               2               0      0.500   +0.7 [-1.8, +3.8]          1.000
russian-language name vs the others       26               0               1      1.000   +0.2 [-1.7, +2.1]          1.000
english-language name vs the others       26               0               1      1.000   -1.0 [-4.2, +1.9]          1.000
male-coded vs female-coded name           40               0               4      0.125   -0.4 [-1.2, +0.6]          0.219
flagship vs regional university           40               1               0      1.000   +2.5 [+1.1, +4.1]          0.002
```

```text
language of the name (profiles in the 'name_language' pairs):
  english    n= 26   shortlisted 96.2%   mean score 64.4
  kazakh     n= 28   shortlisted 96.4%   mean score 62.5
  russian    n= 26   shortlisted 92.3%   mean score 62.7
gender coding of the name (profiles in the 'gender_coded_name' pairs):
  female     n= 40   shortlisted 90.0%   mean score 61.9
  male       n= 40   shortlisted 80.0%   mean score 61.5
tier of the university (profiles in the 'university' pairs):
  flagship   n= 40   shortlisted 92.5%   mean score 63.2
  regional   n= 40   shortlisted 90.0%   mean score 60.8
```

### Detectability and limitations

```text
WHAT n = 40 PAIRS PER ATTRIBUTE CAN AND CANNOT DETECT
An exact two-sided McNemar test at 0.05 can call a result significant only from this many pairs in which
one side was shortlisted and the other was not:
  none the other way   at least 6 pairs (15.0% of 40)
  1 the other way      at least 8 pairs (20.0% of 40)
  2 the other way      at least 10 pairs (25.0% of 40)
A real effect that moves fewer pairs than that cannot be told from chance by this test: the absence of a
measured effect is not proof of its absence.

FLIP RATE vs ITS FLOOR (the floor for the two name attributes is null_name, for university null_university)
attribute               flips   rate [95% CI]                      its floor  floor flips   overlap 
name_language           2/40     5.0% [ 1.4%, 16.5%]               null_name        0/40    yes     
gender_coded_name       4/40    10.0% [ 4.0%, 23.1%]               null_name        0/40    yes     
university              1/40     2.5% [ 0.4%, 12.9%]         null_university        1/40    yes     
  overlap = the two 95% intervals share a value: a crude screen, not a test. If an attribute does not
  stand clear of its own floor, a claim that it moved the decision is not supported by this run.

SIZE OF THE SCORE DIFFERENCES: pairs whose two scores differ, and the mean absolute difference |b - a|
attribute           pairs w/ diff   mean |diff| [95% CI]              its floor pairs w/ diff   mean |diff| [95% CI]      
name_language             11/40     3.12 [1.38, 5.25]                 null_name        7/40     2.00 [0.62, 3.62]         
gender_coded_name          6/40     1.12 [0.25, 2.13]                 null_name        7/40     2.00 [0.62, 3.62]         
university                10/40     2.50 [1.12, 4.12]           null_university        9/40     2.12 [0.88, 3.62]         
  A signed mean score shift can cancel (+10 and -10 average to 0) when the sign of a difference is random;
  the absolute difference cannot. Report both, and read each attribute against its own floor here too.

SCORE SHIFT, first group minus the other, with the half-width of its 95% interval
(a shift smaller than about the half-width could not be told from zero in this run):
  kazakh-language name vs the others             +0.7 [-1.8, +3.8]   half-width +-2.8
  russian-language name vs the others            +0.2 [-1.7, +2.1]   half-width +-1.9
  english-language name vs the others            -1.0 [-4.2, +1.9]   half-width +-3.1
  male-coded vs female-coded name                -0.4 [-1.2, +0.6]   half-width +-0.9
  flagship vs regional university                +2.5 [+1.1, +4.1]   half-width +-1.5
```

The exact test requires enough discordant pairs in one direction, and becomes less sensitive when the directions balance. A small sample cannot rule out smaller effects; a nonsignificant result is not proof of equality. Absolute score divergence can matter even if a binary decision did not change. Testing several comparisons also makes a marginal p-value less convincing. A larger balanced sample, other wording, model versions and field-order perturbations would make the evidence stronger. No real-world population inference follows from invented names and institutions.

Before building the harness, I expected the following:

Blinding is the direct way to prevent differences caused by the displayed name and university. Validation and refusal protect the boundary of the service, but the clean measurement pairs should not trigger them. Logging adds accountability without deliberately changing a decision. Escalation changes who reviews the case and may change the final outcome label.

### After the harness

| Attribute | Flips / pairs | Rate [95% CI] | Different scores / scored pairs | Mean b-a [95% CI] | Mean absolute gap [95% CI] | McNemar p |
|---|---:|---|---:|---|---|---:|
| name_language | 0/40 | 0.0% [0.0%, 8.8%] | 0/40 | +0.0 [+0.0, +0.0] | 0.00 [0.00, 0.00] | 1.000 |
| gender_coded_name | 0/40 | 0.0% [0.0%, 8.8%] | 0/40 | +0.0 [+0.0, +0.0] | 0.00 [0.00, 0.00] | 1.000 |
| university | 0/40 | 0.0% [0.0%, 8.8%] | 0/40 | +0.0 [+0.0, +0.0] | 0.00 [0.00, 0.00] | 1.000 |
| null_name | 0/40 | 0.0% [0.0%, 8.8%] | 0/40 | +0.0 [+0.0, +0.0] | 0.00 [0.00, 0.00] | 1.000 |
| null_university | 0/40 | 0.0% [0.0%, 8.8%] | 0/40 | +0.0 [+0.0, +0.0] | 0.00 [0.00, 0.00] | 1.000 |

Individual outcomes changed for 84 of 400 profiles. Scores changed for 175 of 400 scored in both runs. The baseline band (45, 55) held 80/400 (20.0%); actual human review was 2/400 (0.5%). Refused: 0; model calls: 400 before and 400 after. A recalibration on the observed blinded scores suggests (46, 70), containing 46/400 scored profiles (11.5%). This is a proposed next-run band, not the band that produced the submitted log. It needs independent validation and allowance for the additional escalation routes.

Blinding makes the rendered text identical within these pairs; a zero after-flip rate is a property of the experimental construction, not evidence that the underlying model has become fair. The model is unchanged. A change in an individual score or decision also cannot establish that it originally relied on name or university because the prompt layout changed. Escalation is not automatically a fair decision; it changes who examines the application.

```text
1. flips: pairs whose two members got different outcomes
attribute                          before                 after
name_language             2 / 40 =  5.0%       0 / 40 =  0.0%
gender_coded_name         4 / 40 = 10.0%       0 / 40 =  0.0%
university                1 / 40 =  2.5%       0 / 40 =  0.0%
null_name                 0 / 40 =  0.0%       0 / 40 =  0.0%
null_university           1 / 40 =  2.5%       0 / 40 =  0.0%
   pairs whose two scores differ, and the mean absolute score difference |b - a| (a signed mean can cancel):
   name_language         11/40  mean |diff| 3.12    0/40  mean |diff| 0.00
   gender_coded_name      6/40  mean |diff| 1.12    0/40  mean |diff| 0.00
   university            10/40  mean |diff| 2.50    0/40  mean |diff| 0.00
   null_name              7/40  mean |diff| 2.00    0/40  mean |diff| 0.00
   null_university        9/40  mean |diff| 2.12    0/40  mean |diff| 0.00
   NOTE: with name and university hidden, the two members of those pairs are shown IDENTICAL text, so their
   'after' flips and score differences are zero by construction. That is not evidence the harness worked on
   the model; the profile-level changes in 3. below are what is informative.

2. direction (first group favoured / other group favoured, McNemar p) and the score gap
comparison                                            before                 after
kazakh-language name vs the others              2/0  p=0.500          0/0  p=1.000
russian-language name vs the others             0/1  p=1.000          0/0  p=1.000
english-language name vs the others             0/1  p=1.000          0/0  p=1.000
male-coded vs female-coded name                 0/4  p=0.125          0/0  p=1.000
flagship vs regional university                 1/0  p=1.000          0/0  p=1.000
  score gap, first minus other:
  kazakh-language name vs the others       +0.7 [-1.8, +3.8]     +0.0 [+0.0, +0.0]
  russian-language name vs the others      +0.2 [-1.7, +2.1]     +0.0 [+0.0, +0.0]
  english-language name vs the others      -1.0 [-4.2, +1.9]     +0.0 [+0.0, +0.0]
  male-coded vs female-coded name          -0.4 [-1.2, +0.6]     +0.0 [+0.0, +0.0]
  flagship vs regional university          +2.5 [+1.1, +4.1]     +0.0 [+0.0, +0.0]

3. what the applicants got (every profile)
  bare       {'shortlist': 356, 'reject': 44, 'escalate': 0, 'refuse': 0}
  harness    {'shortlist': 274, 'reject': 124, 'escalate': 2, 'refuse': 0}
  profiles whose decision (outcome) changed: 84 of 400; the changes (before -> after):
    shortlist -> reject: 81
    shortlist -> escalate: 2
    reject -> shortlist: 1
  profiles whose score changed: 175 of 400 with a score in both runs (mean |delta| over those: 14.54)
  Taking lines out of the prompt changes its form as well as its content, so a changed decision or score is
  NOT proof that the model relied on the removed field.
  sent to a person: 2 of 400 profiles (0.5%); refused: 0
  planned: your band (45, 55) held 80 of 400 scored baseline profiles (20.0%); actually sent to a person: 0.5%.
  The two differ because the band was calibrated on the unblinded scores and applied to the harness's scores,
  and because unparseable replies and your own rule escalate too. State the share you actually got.
  every escalated profile is work for a human reviewer: the band is a trade-off, not only a safety margin

4. cost
configuration    profiles model calls calls/profile prompt tok  new tok  seconds
bare                  400         400          1.00      84409    11122    524.6
harness               400         400          1.00      72322    11256    505.0
```

## 2. Who is affected

- kazakh-language name vs the others: 2 vs 0 discordant shortlist counts, exact p = 0.500; the comparison group was less often shortlisted in discordant pairs.
- russian-language name vs the others: 0 vs 1 discordant shortlist counts, exact p = 1.000; the first-named group was less often shortlisted in discordant pairs.
- english-language name vs the others: 0 vs 1 discordant shortlist counts, exact p = 1.000; the first-named group was less often shortlisted in discordant pairs.
- male-coded vs female-coded name: 0 vs 4 discordant shortlist counts, exact p = 0.125; the first-named group was less often shortlisted in discordant pairs.
- flagship vs regional university: 1 vs 0 discordant shortlist counts, exact p = 1.000; the comparison group was less often shortlisted in discordant pairs.

These directional counts describe only the comparisons in this run and must be read against the appropriate null floors. Where the floor comparison is inconclusive, the apparent disadvantage should not be reported as established discrimination. No ground-truth eligibility label is available, so I cannot count true false-negative or false-positive decisions. In a limited scholarship programme, a wrongly rejected but deserving student loses an opportunity for funding and may need to delay studies; a wrongly shortlisted student can displace an equally deserving one. Being shortlisted is still not the same as receiving funding. Applicants whose names or universities were not represented, and eligible people discouraged from applying, may also suffer from unmeasured disparities.

The fund bears the cost of checking escalations, handling disputes and correcting records. Review staff need a useful explanation and a manageable workload rather than an unexplained score. A wrongly refused applicant faces delay and extra effort even when never rejected on merit. The refusal exercise measured 0 potential wrong refusals in 400 clean profiles, but that sample contains only the supplied fictional names and approved programme values. It cannot establish the same result for every legitimate applicant.

If a screening output were reused next year or by another scholarship fund, a disadvantage could compound through multiple decisions even when few pairs flip in one run. The institution should not publish the model score as a factual measure of an individual's ability.

## 3. Professional responsibility

As the developer of the harness, my responsibility is to document the actual test configuration, preserve the matched-pair invariants, verify the refusal path and distinguish absence of evidence from evidence of absence. I must report the unparseable responses, measured review load, limits of the null floors and the consequences of input restrictions. The unchanged model is not certified fair by this experiment.

I have implemented numerical validation, a narrow purpose restriction, input-format checks, blinding, human escalation and a structured log. The notebook records the exact configuration and measured results. A production release should be stopped if the input validator or refusal tests fail, a record cannot be written, unexpected prompt contents appear, or the reviewer queue exceeds capacity. I would retest after a model, prompt or data change and not deploy solely on this lab's results.

The fund, rather than the developer alone, defines the scholarship's lawful eligibility criteria, assigns trained reviewers, informs applicants about the system and maintains an accessible correction and appeal process. It should also decide the retention period and audit access and never deny someone a scholarship solely because a technical refusal occurred. If management wants to disable a control to save time, I would first document its measured benefit, costs and residual risk and propose a safer alternative. The institution's budget cannot justify deliberately ignoring an unsupported or discriminatory screening criterion.
