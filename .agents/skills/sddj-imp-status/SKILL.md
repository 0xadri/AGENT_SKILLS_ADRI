---
name: sddj-imp-status
description: Add/Update implementation status to a document
---

Process each file listed in: $ARGUMENTS

For each file, add/update its "Implementation Status" section with a title and description.

**Possible Values For Title:**

- READY FOR IMPLEMENTATION
- PARTLY IMPLEMENTED
- PARTLY IMPLEMENTED: BACKEND COMPLETE, FRONTEND API READY, FRONTEND UI PENDING
- FULLY IMPLEMENTED

**Description:**

- if plan, add a quick point-by-point overview
- if spec, add a reference to the separate `_plan.md`

**Example for plan doc:**

```
## IMPLEMENTATION STATUS: FULLY IMPLEMENTED

**Backend**: ✅ Fully implemented - all phases complete (validation, repository, service, controller, trip enrichment, tests)
**Frontend**: ✅ Fully implemented - all phases complete
**Testing**: ✅ Fully implemented - all phases complete (unit tests, integration tests, e2e tests)

---
```

**Example for spec doc:**

```
## IMPLEMENTATION STATUS: FULLY IMPLEMENTED

See the separate `user_reviews_plan.md` for detailed implementation tracking.

---
```
