---
date: 2018-09-03
title: "Yubikey and shift key on French or Belgian keyboards"
---

For readers with Belgian or French keyboards.

If you configure your Yubikey by default with HOTP, when you touch your Yubikey, the output gives characters instead of digits (like "&é"'(§").

You have two options:

- You can press the `shift` key when you touch your Yubikey 
- You can configure the keymap, using `ykpersonalize`

Option 2 can be achieved with the following command, taken from the documentation:

`ykpersonalize -S06050708090a0b0c0d0e0f111517181986858788898a8b8c8d8e8f9195979899a79e9fa0a1a2a3a4a5a6382b28 -y`