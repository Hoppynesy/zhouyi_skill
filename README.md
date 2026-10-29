[README.md](https://github.com/user-attachments/files/34111863/README.md)

# 周易 skill / I Ching Skill

> 把《周易》做成可执行的 AI skill：起卦、定主辞，朱熹《本义》与王弼注孔疏《正义》两家并列解读，不做确定性预言。
>
> An executable *I Ching* skill: cast a hexagram, resolve the primary line, and read it through both Zhu Xi's *Benyi* and Wang Bi/Kong Yingda's *Zhengyi* — no fortune-telling.

**[中文](#中文) · [English](#english)**

---

## 中文

《周易》在这里不是算命，是一套**结构化的处境反思工具**：帮人看清局面、权衡进退、提示风险，而不是替人下结论。

### 两家怎么读

| | 朱子《本义》 | 王弼注 · 孔颖达疏《正义》 |
|---|---|---|
| 侧重 | 占断，重「占者如是」的条件 | 义理，重处境与位置 |
| 解释链 | 象 → 占 → 戒 | 时 → 位 → 应 |
| 语气 | 此卦／此爻主…… | 此时主…… |

两家对同一爻的读法不同时（例如某爻是不是「寇」），会写进「两家异同」，不写成一致。

### 它怎么工作

```
问题 → cast.py 起卦 → zhuci.py 定主辞 → 两家能力卡解读 → 汇总 → journal 记录
```

- **起卦**：`cast.py`（yarrow / coins / manual），随机源用 `secrets`；
- **排卦**：`derive.py`，本卦、变卦、互卦、错卦、综卦；
- **取辞**：`zhuci.py`，依《易学启蒙·考变占》，多爻变按前十卦主贞、後十卦主悔；
- **解读**：`references/books/{benyi,zhengyi}` 下的能力卡；
- **记录**：每次写入 `journal/YYYY-MM.md`，存 `lines`，日后用 manual 重报可复现同一卦。

### 安装

DeepSeek Harness：

```powershell
git clone https://github.com/<you>/zhouyi-skill "$env:USERPROFILE\.dsh\skills\zhouyi"
```

或 clone 到任意位置，再建 junction：

```powershell
cmd /c mklink /J "%USERPROFILE%\.dsh\skills\zhouyi" "D:\path\to\zhouyi-skill"
```

其他宿主：把仓库放进 skill 目录，或让 agent 直接读 `SKILL.md`。运行时不依赖第三方 Python 包（Python 3.10+）。

### 用法

直接说人话：

- 「帮我起一卦，问要不要接这个项目。」
- 「我摇了 9 8 8 8 7 8，帮我解一下。」
- 「屯卦六三为什么是往吝？」

命令行：

```powershell
$env:PYTHONUTF8='1'
python scripts\cast.py yarrow
python scripts\cast.py coins
python scripts\cast.py manual 9 8 8 8 7 8
python scripts\zhuci.py 9 8 8 8 7 8
```

### 输出长什么样

```
【卦】本卦 咸（#31）之 夬（#43）｜变爻：初六、六二｜起卦：三钱法
【主辞】「六二：咸其腓，凶，居吉。」（#31.2，主）
【取辞规则】二爻变：占本卦二变爻爻辞，以上爻为主

【朱子《本义》】结论 / 依据原文 / 象→占→戒
【王弼注孔疏《正义》】结论 / 依据原文 / 时→位→应
【两家异同】一致处……；分歧处……
【建议】2–3 条，注明出自哪家
【引申】单独标，不与注义混
```

### 引文与底本

- 所有「」引文回底本核对，零排除；当前：《本义》88 条、《正义》77 条，全部命中。
- 底本：《周易本義》（四庫全書本）、《周易正義》。
- 《正义》未附《周易略例》，相关处标「疏引」或「据注文归纳」，不冒充王弼原文。
- 王注与孔疏分标；系辞、说卦、序卦、杂卦的注者是韩康伯。

### 目录

```
SKILL.md              入口与输出契约
scripts/cast.py       起卦
scripts/derive.py     排卦
scripts/zhuci.py      取辞
data/hexagrams.json   六十四卦经文
references/books/     两家能力卡与概览
tests/                单测与 e2e 用例
journal/              占问记录（运行时生成）
```

### 测试

```powershell
$env:PYTHONUTF8='1'
python -m unittest discover -s tests
```

23 个单测；`tests/e2e_cases.md` 收录三条端到端用例。

### 边界

- 仅古法模式；六爻纳甲未接入，不给应期与成败断语。
- 医疗、法律、投资、人身安全：只给反思角度，不替代专业判断。
- 同一事反复占问，按「渎则不告」停止。
- 不做确定性预言。

### 授权

代码 MIT；经文与注疏取自维基文库，属公有领域。

---

## English

The *I Ching* here is not fortune-telling. It is a **structured way to think through a situation**: see the shape of the moment, weigh options, notice risk — not a machine for handing down verdicts.

### Two readings, side by side

| | Zhu Xi, *Benyi* | Wang Bi (comm.) & Kong Yingda (subcomm.), *Zhengyi* |
|---|---|---|
| Focus | Divination; the condition "if the consulter is thus" | Philosophical reading; situation and position |
| Chain | image → divination → caution | time → position → correspondence |
| Voice | "This hexagram/line governs…" | "At this time, the ruling theme is…" |

When the two disagree on a line (e.g. whether a line is a "robber"), the disagreement is stated in a dedicated section — never smoothed into agreement.

### How it works

```
question → cast.py → zhuci.py → two sets of capability cards → synthesis → journal
```

- **Casting**: `cast.py` (yarrow / coins / manual), randomness from `secrets`.
- **Deriving**: `derive.py` — primary, changed, nuclear, opposite and reversed hexagrams.
- **Line selection**: `zhuci.py`, following the *Yijing Meng* change rules; for three moving lines, the first ten hexagrams favour the primary, the latter ten the changed one.
- **Reading**: capability cards under `references/books/{benyi,zhengyi}`.
- **Logging**: every consultation is appended to `journal/YYYY-MM.md` with its `lines`, so the same cast can be reproduced later with manual input.

### Install

DeepSeek Harness:

```powershell
git clone https://github.com/<you>/zhouyi-skill "$env:USERPROFILE\.dsh\skills\zhouyi"
```

Or clone anywhere and link it:

```powershell
cmd /c mklink /J "%USERPROFILE%\.dsh\skills\zhouyi" "D:\path\to\zhouyi-skill"
```

Other hosts: drop the repo into the host's skill directory, or let the agent read `SKILL.md`. No third-party Python packages required (Python 3.10+).

### Usage

Just talk naturally:

- "Cast a hexagram for me — should I take this project?"
- "I got 9 8 8 8 7 8, read it for me."
- "Why does line 3 of Zhun say going brings regret?"

Or use the CLI:

```powershell
$env:PYTHONUTF8='1'
python scripts\cast.py yarrow
python scripts\cast.py coins
python scripts\cast.py manual 9 8 8 8 7 8
python scripts\zhuci.py 9 8 8 8 7 8
```

### Output shape

```
【Hexagram】Xian (31) → Guai (43) | moving: 1, 2 | method: coins
【Primary line】"Xian its calf. Misfortune. Staying put brings good fortune." (31.2)
【Rule】two moving lines: read both, the upper one leads

【Zhu Xi, Benyi】conclusion / sources / image → divination → caution
【Wang Bi & Kong Yingda, Zhengyi】conclusion / sources / time → position → correspondence
【Where they differ】agreements…; disagreements…
【Suggestions】2–3 items, each attributed to one school
【Extension】marked separately, never mixed with the commentaries
```

### Sources and verification

- Every quotation in 「」 is checked against the source text; zero exclusions. Currently: 88/88 in *Benyi*, 77/77 in *Zhengyi*.
- Base texts: *Zhouyi Benyi* (Siku Quanshu edition) and *Zhouyi Zhengyi*.
- *Zhengyi* does not include the *Zhouyi Lueli*; such methods are marked "cited in the subcommentary" or "inferred from the commentary", never passed off as Wang Bi's own words.
- Wang Bi's commentary and Kong Yingda's subcommentary are labelled separately; the commentary on the Xici, Shuogua, Xugua and Zagua is by Han Kangbo.

### Layout

```
SKILL.md              entry point and output contract
scripts/cast.py       casting
scripts/derive.py     hexagram derivation
scripts/zhuci.py      line selection
data/hexagrams.json   the 64 hexagrams
references/books/     capability cards for both readings
tests/                unit tests and e2e cases
journal/              consultation log (created at runtime)
```

### Tests

```powershell
$env:PYTHONUTF8='1'
python -m unittest discover -s tests
```

23 unit tests; `tests/e2e_cases.md` holds three end-to-end cases.

### Boundaries

- Traditional (gu fa) mode only; six-line najia divination is not included, and no timing or success/failure verdicts are given.
- Medical, legal, financial and personal-safety questions: reflective angles only, never a substitute for professional advice.
- Repeatedly consulting on the same matter is declined, per "the first consultation is answered; the second and third are not".
- No deterministic predictions.

### License

Code under MIT. Classical texts and commentaries are in the public domain (via Wikisource).
