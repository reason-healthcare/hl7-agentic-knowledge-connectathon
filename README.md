# AI-Assisted Development of Clinical Reasoning Knowledge

This repository contains the shared material for a proposed HL7 FHIR
Connectathon track. The track tests whether AI-assisted authoring can transform
the same unstructured clinical sources into valid, clinically coherent, and
interoperable knowledge artifacts.

The repository is deliberately implementation-neutral. It does not prescribe
an agent framework, model, workflow, or vendor product.

## Track question

Can AI-assisted development reduce authoring and review effort, support a
repeatable process across artifact types, and preserve the quality required for
clinical reasoning content?

The track records process evidence and artifact evidence separately:

- **Process:** elapsed authoring time, human review time, revision cycles,
  blocking issues, and repeatability.
- **Artifacts:** source traceability, FHIR conformance, CQL translation and
  execution, clinical coherence, and cross-implementation agreement.

## Seed use case

For a community-dwelling adult age 65 years or older seen in ambulatory care:

1. Is the patient in scope for fall-risk screening?
2. Was the CDC STEADI three-question screen completed?
3. Does the completed screen indicate increased fall risk?
4. Which evidence-linked action is applicable?
5. Across a measurement period, what proportion of eligible patients completed
   the screen?

The three screening questions ask whether the patient:

- feels unsteady when standing or walking;
- worries about falling; or
- has fallen in the past year.

For this track, a completed screen indicates increased fall risk when any
answer is yes. Missing or incomplete answers remain unknown and must not be
treated as a negative screen.

The track includes exercise guidance linked to the USPSTF grade B
recommendation and individualized consideration of multifactorial
interventions linked to the grade C recommendation. It does not diagnose a
fall-related condition, select a specific treatment, or order an intervention.

## Repository contents

- [Source manifest](sources/manifest.yaml) and [raw source snapshots](sources/raw/)
- [MARP track overview](slides/connectathon-track-overview.md) and
  [rendered PDF](slides/connectathon-track-overview.pdf)
- [Synthetic patient test bundles](test-bundles/README.md), a shared coded
  [Questionnaire](test-bundles/questionnaire.json), SDC extraction
  expectations, assertions, and a case summary for every bundle

The raw corpus contains only snapshots whose current redistribution basis is
recorded in the manifest. Standards references and CMS139FHIR remain link-only.
CMS139FHIR is a comparison point; it is not copied, and the track-authored
measure must not claim equivalence to it.

## Participant task

Participants receive the source corpus, this overview, and the shared test
bundles. They may use any AI-assisted or conventional development approach.
A submission should record its tools, versions, instructions, human decisions,
and unresolved issues.

The expected output includes:

| Knowledge role | Expected artifact |
| --- | --- |
| Evidence and population definitions | `Evidence`, `EvidenceVariable` |
| Three-question assessment | `Questionnaire`, reusable logic |
| Recommendation workflow | `PlanDefinition`, `ActivityDefinition`, `Library` |
| Screening-completion measure | `Measure`, `Library` |
| Terminology and package metadata | `ValueSet`, `ImplementationGuide` |
| Executable logic | CQL source and ELM JSON |
| Distribution | FHIR R4 NPM package with exact dependency versions |

The input fixtures also exercise the SDC Observation-based extraction pattern.
Each completed QuestionnaireResponse references the same versioned,
LOINC-coded Questionnaire. Its expected extracted Observations are supplied as
a normalized resource set so participants can compare `$extract` behavior
across implementations without depending on server-specific transaction
details. Incomplete and absent responses remain explicit negative extraction
cases.

FHIR R4 does not define `Citation`; an R4 baseline submission should use
available provenance and related-artifact elements. Any R5 variant must be
clearly separated from the R4 baseline.

## Required executable behavior

The shared assertions use these expression names:

- `In Screening Population`
- `Completed Three Question Screen`
- `At Increased Fall Risk`
- `Exercise Intervention Applicable`
- `Consider Multifactorial Intervention`
- `Initial Population`
- `Denominator`
- `Numerator`

The first five expressions describe patient-level assessment and guidance. The
last three describe a track-authored screening-completion process measure.
Incomplete or absent screening data produces an unknown increased-risk result,
while the measure numerator remains false.

## Interoperability test

The baseline is FHIR R4 4.0.1, ELM JSON, and a conservative CQL subset suitable
for multiple CQL 1.5-era implementations. Participants:

1. validate the generated FHIR resources and package;
2. store and retrieve the knowledge artifacts from independent FHIR servers;
3. translate the same CQL and record diagnostics;
4. execute every shared patient bundle;
5. compare Boolean and null semantics against the assertions; and
6. record exact implementation, dependency, terminology, and artifact versions.

Baseline evidence requires agreement from at least two independent CQL
implementations and round-trip testing in at least two FHIR implementations.
Compilation or upload alone is not a passing result.

## Review boundaries

Every clinical statement should distinguish source content from track-authored
operational choices and retain a stable source locator. FHIR validation and CQL
compilation are necessary, but they are not clinical approval.

All patient data in this repository is synthetic. The material is for
Connectathon testing and education, not clinical use.
