# Bug Reporting Checklist

Bugs will inevitably be found in our software, whether internally by ourselves or from Po.et users. The checklists below will be introduced into the public-facing repos so that bug reporters understand what they need to do to file a helpful and complete bug report (see the **User Checklist** below).

Once a bug report has been filed, the **QE Review Checklist** will be used internally to ensure the bug is triaged correctly and that any bugs related to security are escalated. The following [sequence diagram](https://mermaidjs.github.io/mermaid-live-editor/#/view/eyJjb2RlIjoic2VxdWVuY2VEaWFncmFtXG5cbnBhcnRpY2lwYW50IFUgYXMgVXNlclxucGFydGljaXBhbnQgQ1IgYXMgQ29kZSBSZXBvIElzc3VlIFRyYWNrZXJcbnBhcnRpY2lwYW50IFFFIGFzIFFFIFRlYW1cbnBhcnRpY2lwYW50IEQgYXMgRGV2IFRlYW1cblxuVS0-PlU6IERpc2NvdmVycyBidWdcbm5vdGUgb3ZlciBVOiBVc2VyIGZpbGxzIGluPGJyPmJ1ZyByZXBvcnQgdGVtcGxhdGVcblUtPj5DUjogT3BlbnMgaXNzdWUgcmVwb3J0aW5nIGJ1Z1xuQ1ItPj5RRTogTm90aWZpY2F0aW9uIG9mIG5ldyBidWcgcmVwb3J0XG5RRS0-PlFFOiBUcmlhZ2UgaXNzdWUgd2l0aCBidWcgcmVwb3J0IGNoZWNrbGlzdFxubm90ZSBvdmVyIFFFOiBFc2NhbGF0ZSBzZWN1cml0eTxicj5yZWxhdGVkIHJlcG9ydHNcblFFLT4-VTogRm9sbG93IHVwIHdpdGggdXNlciBmb3IgaW5jb21wbGV0ZSByZXBvcnRzIChvciBjbG9zZSB1c2VsZXNzIG9uZXMpXG5RRS0-PkQ6IFNlbmQgcmVhbCBidWdzIGZvciBmdXJ0aGVyIGFuYWx5c2lzXG5ELT4-VTogRm9sbG93cyB1cCBmb3IgbW9yZSBkZXRhaWxlZCByZXBvcnRpbmcgaWYgbmVlZGVkXG5ELT4-UUU6IFJlc29sdmVzIGJ1Z1xuUUUtPj5RRTogVGVzdHMgZml4XG5RRS0-PkNSOiBVcGRhdGVzL2Nsb3NlcyBpc3N1ZVxuQ1ItPj5VOiBOb3RpZmljYXRpb24gb2YgcmVzb2x2ZWQgaXNzdWVcbiIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In19) shows the flow for handling bug reports.

## User Checklist

1. What result were you expecting?
1. What result did you actually observe, and what leads you to believe this indicates a bug?
1. How can you reliably reproduce the observed result?
    1. Versions of all apps/packages/testing tools involved
    1. Any background information on the environment where or about the circumstances in which the bug occurred (e.g., are you behind a corporate firewall)
    1. Ordered list of exact steps to reproduce the observed result (screenshots/videos are welcome only if they supplement the list - do not send them in by themselves without written instructions for reproducing the bug)
1. Have you tested the steps for reproduction in 3.3 above to confirm they reliably produced the bug? If they do not, we will not be able to troubleshoot the issue.

## QE Review Checklist

1. Is the issue a potential security vulnerability (if so, escalate and treat with highest priority)?
1. Has the user provided examples of both the expected result and the actual result?
    1. Do the examples infer, or has the user provided an explanation of why they indicate, a bug?
1. Has the user provided a detailed description on how to reliably reproduce the bug?
    1. Version of all apps/packages involved
    1. Exact circumstances that caused the bug (include mention of any tools used, local settings such as a firewall, etc.)
    1. Ordered list of steps to reproduce (screenshots/video are encouraged to supplement the list, but not replace it)
1. Can you reproduce the issue by following the steps provided by the user?
