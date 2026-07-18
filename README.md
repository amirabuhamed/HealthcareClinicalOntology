# Healthcare Clinical Ontology

Knowledge Representation exam project, Prof. Matteo Cristani
University of Verona
Amir Abuhamed

Implemented in OWL 2 using Protégé, as required for the course.

## Ontology Category

This is a domain ontology, modeling a patient's care pathway: diagnosis,
treatment and follow-up, along with the institutional context around it
(hospitals, departments, prescriptions, insurance, lab tests). I kept the
scope to this one connected pathway instead of trying to cover all of
healthcare, so the classes and roles actually fit together instead of being
a random list of terms.

## What's Modeled

- Patients and practitioners (doctors, nurses, specialists)
- Diseases (chronic vs. acute), symptoms, diagnoses
- Treatments (surgery, therapy, pharmacotherapy) and medications
- Hospitals, clinics, departments
- Appointments, admissions, lab tests, prescriptions, medical records
- Allergies, vital signs, insurance coverage

## Classes and Roles

33 classes in total: 29 primitive classes (Patient, Practitioner, Doctor,
Nurse, Specialist, Disease, ChronicDisease, AcuteDisease, Symptom,
Diagnosis, Treatment, Surgery, Therapy, Pharmacotherapy, Medication,
Hospital, Clinic, Department, Appointment, Admission, LabTest, Prescription,
MedicalRecord, InsurancePlan, Allergy, VitalSign, and the abstract roots
Person, Institution, ClinicalEvent), plus 4 defined classes that the
reasoner fills in on its own: ChronicPatient, SurgicalPatient,
HospitalizedPatient, AllergicPatient.

50 roles in total: 29 object properties (hasSymptom, diagnosedWith,
diagnosisOf, treatedBy/treats, undergoesTreatment, prescribes,
prescribedMedication, admittedTo, hasAppointment, hasMedicalRecord,
hasAllergy, hasInsurance, hasVitalSign, interactsWith, and others linking
practitioners, institutions and clinical events) and 21 data properties
(patientName, dateOfBirth, diagnosisDate, severityLevel, dosage,
treatmentCost, and similar attributes).

Disjointness is declared between Patient/Practitioner, Doctor/Nurse,
Hospital/Clinic, ChronicDisease/AcuteDisease, and the three Treatment
subclasses (Surgery, Therapy, Pharmacotherapy), so the reasoner doesn't
let an individual be classified as two mutually exclusive things at once.

## Design Decisions

**Person as an abstract root.** Patient and Practitioner both sit under it,
but are disjoint from each other, so nobody can be modeled as both at the
same time.

**Treatment split into Surgery, Therapy and Pharmacotherapy.** This is what
makes SurgicalPatient mean something specific. If Treatment weren't split,
any treatment at all would trigger it.

**Diagnosis kept separate from Disease.** Diagnosis is the clinical record
(it has a date, a severity level), Disease is the condition itself, like
Type2Diabetes. That way one Disease can be linked from many patients'
diagnoses.

**A few functional and symmetric roles.** hasMedicalRecord, hasInsurance,
dateOfBirth, licenseNumber and policyNumber are functional (at most one
value). interactsWith is symmetric, since drug interactions go both ways.
treatedBy and treats are inverses of each other, just to show the inverse
role construct without duplicating every property in the ontology.

## Defined Classes: a Worked Example

Four classes aren't asserted directly on any individual, they're defined
through necessary-and-sufficient conditions, so the reasoner classifies
individuals into them by itself:

```
ChronicPatient      ≡ Patient ⊓ ∃diagnosedWith.(Diagnosis ⊓ ∃diagnosisOf.ChronicDisease)
SurgicalPatient     ≡ Patient ⊓ ∃undergoesTreatment.Surgery
HospitalizedPatient ≡ Patient ⊓ ∃admittedTo.Admission
AllergicPatient     ≡ Patient ⊓ ∃hasAllergy.Allergy
```

In the A-Box, JohnSmith is only ever typed as Patient. He's diagnosedWith a
Diagnosis that's diagnosisOf Type2Diabetes, which is a ChronicDisease, so
the reasoner infers him as a ChronicPatient without that ever being stated
directly. He's also undergoesTreatment InsulinTherapy, which is a
Pharmacotherapy, not a Surgery, so he's not inferred as SurgicalPatient.
That's a small but useful demo of the reasoner actually distinguishing
between cases instead of just repeating what was asserted.

## Conclusion

The aim was to go beyond a flat taxonomy and actually use the DL machinery
from the course: existential and value restrictions, a qualified number
restriction, functional and symmetric roles, inverse roles, disjointness,
and defined classes, so a reasoner has real work to do on this ontology.
