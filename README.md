# [Balanced-COPA](https://balanced-copa.github.io)

---

## Balanced COPA

To prevent models from exploiting superficial cues in COPA, we introduce
**Balanced COPA**.

Balanced COPA contains one additional,
_mirrored_ instance for each original training instance.
This mirrored instance uses the same alternatives as the corresponding original
instance, but introduces a new premise which matches the _wrong_ alternative
of the original instance, e.g. _The man hid his feet_, for which the correct
alternative is now because _He got a hole in his sock_.
Since each alternative occurs exactly once as correct answer and exactly once as
wrong answer in Balanced COPA, the lexical distribution between correct and wrong
answers is perfectly balanced, i.e., superficial cues in the original alternatives
have become uninformative.

Balanced COPA allows us to study the impact of the presence or absence of
superficial cues on model performance
