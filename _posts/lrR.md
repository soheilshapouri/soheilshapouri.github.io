---
layout: post
title: "Notes on Simple and Multiple Logistic Regression"
date: 2024-11-22
category: [codes]
excerpt: "Decoding the odds, one logit at a time."
---
If you have binary response (e.g., Natural vs. Technological) logistic regression can be a good choice for modeling. But remember two things: For resppnses with more than two categories, you can use multinomial logistic regression but it does not give you reliable estimates and its too many assumptions might be difficult to check. Moreover, make sure the response is actually binanry not continious as dichotomizing continious variables is a problematic practice. 
