---
description: Adding cache on your Laravel Eloquent queries' results is now a breeze.
---

# ⚡ Introduction

![](.gitbook/assets/untitled\_design\_1\_25.png)

[![CI](https://github.com/renoki-co/laravel-eloquent-query-cache/workflows/CI/badge.svg?branch=master)](https://github.com/renoki-co/laravel-eloquent-query-cache/workflows/CI/badge.svg?branch=master) [![codecov](https://camo.githubusercontent.com/1aba32d42feee413254aa7f526262c81ea9a949b37a0ec26ce17f55162777e99/68747470733a2f2f636f6465636f762e696f2f67682f72656e6f6b692d636f2f6c61726176656c2d656c6f7175656e742d71756572792d63616368652f6272616e63682f6d61737465722f67726170682f62616467652e737667)](https://codecov.io/gh/renoki-co/laravel-eloquent-query-cache/branch/master) [![StyleCI](https://camo.githubusercontent.com/09d8a744efe1a02b2435f02abdc2a8723c0f8bbb98724fbb9a1986cde50e3f69/68747470733a2f2f6769746875622e7374796c6563692e696f2f7265706f732f3232333233363738352f736869656c643f6272616e63683d6d6173746572)](https://github.styleci.io/repos/223236785) [![Latest Stable Version](https://camo.githubusercontent.com/fe6604502be22b0f34cfa7423c6b1bd454e8366dccd8246104ef95153e7ec7d9/68747470733a2f2f706f7365722e707567782e6f72672f72656e6e6f6b6b692f6c61726176656c2d656c6f7175656e742d71756572792d63616368652f762f737461626c65)](https://packagist.org/packages/rennokki/laravel-eloquent-query-cache) [![Total Downloads](https://camo.githubusercontent.com/a4453780b00bf1378c6e069a25d7bd920d8f83ae8570ba411846c563a645c511/68747470733a2f2f706f7365722e707567782e6f72672f72656e6e6f6b6b692f6c61726176656c2d656c6f7175656e742d71756572792d63616368652f646f776e6c6f616473)](https://packagist.org/packages/rennokki/laravel-eloquent-query-cache) [![Monthly Downloads](https://camo.githubusercontent.com/e86229b969348ae7658986ec8583b7dbb4b5d406cce53e1a47fdc824c400430e/68747470733a2f2f706f7365722e707567782e6f72672f72656e6e6f6b6b692f6c61726176656c2d656c6f7175656e742d71756572792d63616368652f642f6d6f6e74686c79)](https://packagist.org/packages/rennokki/laravel-eloquent-query-cache) [![License](https://camo.githubusercontent.com/6262b584f2b933301a91ac539c46823612faf11aadceb0e5b60a47d428416cf0/68747470733a2f2f706f7365722e707567782e6f72672f72656e6e6f6b6b692f6c61726176656c2d656c6f7175656e742d71756572792d63616368652f6c6963656e7365)](https://packagist.org/packages/rennokki/laravel-eloquent-query-cache)

Laravel Eloquent Query Cache brings back the `remember()` functionality that has been removed from Laravel a long time ago. It adds caching functionalities directly on the Eloquent level, helping you acquire speed by caching results.
