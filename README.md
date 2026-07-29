# Smart Cashier

Smart Cashier is a computer vision checkout prototype that detects grocery
products and turns the detections into a reviewable invoice.

It was developed as a university team project. Mohammed Yousef Rasheed focused
on dataset construction, YOLOv8 training and tuning, and the vision checkout
integration. The private source preserves the original contributor credit.

## The Problem

Retail object detection is harder than recognizing a product against a clean
background. Real shelves introduce occlusion, repeated items, reflections,
similar packaging, scale changes, and inconsistent lighting. A useful checkout
system also needs to connect detections to prices and stock without allowing a
model prediction to mutate inventory silently.

Smart Cashier treats detection as one part of a larger human reviewed workflow.

## Architecture

```text
Image upload or camera
          ↓
YOLOv8 product detection
          ↓
Confidence and class review
          ↓
Exact or fuzzy inventory matching
          ↓
Invoice and quantity aggregation
          ↓
Human confirmation
          ↓
Inventory update
```

## Experience

1. Upload one or more product images or use the camera.
2. Review the detections and confidence values.
3. Confirm the items that belong in the basket.
4. Match detected labels to the product database.
5. Generate an invoice in Saudi riyals.
6. Confirm the purchase to update inventory.

## System

* Ultralytics YOLOv8 object detector
* 39 grocery product classes in the private project checkpoint
* Streamlit interface
* exact and fuzzy product matching
* CSV inventory with price and stock
* human confirmation before inventory mutation

## Engineering Decisions

**Human confirmation first**

Detections remain suggestions until the user confirms them. Inventory changes
occur only after the final invoice is reviewed.

**Resilient product matching**

Detected class names are normalized before matching. When exact matching fails,
the application uses RapidFuzz or a deterministic standard library fallback.

**Cached inference resources**

Streamlit caches the YOLO model and inventory loading path so repeated interface
interactions do not reload expensive resources unnecessarily.

**Visible failure behavior**

Unmatched products are shown with a warning and a zero price instead of being
silently assigned to the wrong item.

## Verified Baseline

The private source baseline was checked without modification on 29 July 2026:

* Python compilation passed
* required imports passed
* the bundled YOLO checkpoint loaded successfully
* Streamlit started and returned HTTP 200 from its health endpoint

No purchase confirmation was submitted during verification, so the inventory
file was not changed.

## Source Availability

The complete team source, trained checkpoint, and inventory baseline are
maintained in a private development repository. This public repository documents
the system, contribution, evidence, limitations, and future direction without
redistributing team artifacts.

## Current Limitations

* the CSV inventory is a prototype store, not a concurrent transaction database
* product matching depends on consistency between model class names and inventory names
* the interface does not yet persist a transaction history
* evaluation figures from the project record and checkpoint metadata need one
  reconciled, reproducible evaluation report before public metric claims
* the bundled model is tied to the original custom grocery classes

## Next Technical Milestones

* create a versioned evaluation set with shelf level failure cases
* publish per class precision, recall, and confusion analysis
* replace CSV mutation with transactional inventory storage
* add receipt history, rollback, and audit events
* separate model configuration from interface code

## Responsible Release Notes

Any future source release must preserve complete team attribution, confirm the
training data rights, remove embedded local path metadata if the team approves,
and document the Ultralytics licensing obligations.
