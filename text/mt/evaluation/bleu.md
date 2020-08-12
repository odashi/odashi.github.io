# BLEU (Bilingual Evaluation Understudy)

以下の実装例は元々のBLEUの定義に忠実なものだが，一般的に使用されるツールでは様々な変形が施されており，同じスコアが出るとは限らない．
また言語依存の字句解析も必要である．

実際に機械翻訳の実験を行う際は，自分で実装したコードを使用するのではなく，ツールに頼る方が確実だろう．

```python
import math
import collections

# 文からBLEU統計量を計算
def get_bleu_stats(ref: List[str], hyp: List[str], N: int = 4) -> Dict[str, int]:
  stats = collections.defaultdict(int, {'rl': len(ref), 'hl': len(hyp)})
  N = len(hyp) if len(hyp) < N else N
  for n in range(N):
    matched = 0
    possible = collections.defaultdict(int)
    for k in range(len(ref) - n):
      possible[tuple(ref[k : k + n + 1])] += 1
    for k in range(len(hyp) - n):
      ngram = tuple(hyp[k : k + n + 1])
      if possible[ngram] > 0:
        possible[ngram] -= 1
        matched += 1
    stats['d' + str(n + 1)] = len(hyp) - n
    stats['n' + str(n + 1)] = matched
  return stats

# BLEU統計量からBLEUを計算
def calculate_bleu(stats: Dict[str, int], N: int = 4) -> float:
  np = 0.0
  for n in range(N):
    nn = stats['n' + str(n + 1)]
    if nn == 0:
      return 0.0
    dd = stats['d' + str(n + 1)]
    np += math.log(nn) - math.log(dd)
  bp = 1.0 - stats['rl'] / stats['hl']
  if bp > 0.0: bp = 0.0
  return math.exp(np / N + bp)
  
# 単一リファレンスの場合
ref = ['This', 'is', 'a', 'pen', '.']
hyp = ['There', 'is', 'a', 'pen', '.']
stats = get_bleu_stats(ref, hyp)
bleu = calculate_bleu(stats) # => 0.668740

# 複数リファレンスの場合
stats = collections.defaultdict(int)
for ref, hyp in zip(refs, hyps):
  for k, v in get_bleu_stats(ref, hyp).items():
  stats[k] += v
bleu = calculate_bleu(stats)
```
