<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dsh-certification-hero-en-dark.webp">
  <img src="docs/assets/dsh-certification-hero-en-light.webp" width="100%" alt="dsh-certification: पाँच आयामों का अटेस्टेशन रजिस्ट्री — data/attestations.json में 1 अटेस्टेड रिपॉज़िटरी (PerryLink/dsh-auto-review, grade A, snapshot 2026-11-01) दर्ज है, जो manifest, build hygiene, supply chain (Scorecard 4.1), release integrity और install smoke को कवर करती है; check-freshness.mjs ताज़गी, dsh-cert-mcp मिरर का sha256 और दोनों README मार्कर जाँचता है">
</picture>

# dsh-certification

[English](README.md) · [简体中文](README-zh.md) · [Español](README-es.md) · [Português](README-pt.md) · [हिन्दी](README-hi.md)

</div>

> **`@perrylink/dsh-plugin-certification` से नाम बदला गया।** यह रिपॉज़िटरी अब **`dsh-certification`** है; पुराना scoped नाम अप्रचलित हो चुका है। npm पर कुछ भी प्रकाशित नहीं होता।
[![dshfind](https://dshfind.com/api/badge/PerryLink/dsh-certification?metric=downloads)](https://dshfind.com/plugins/PerryLink/dsh-certification?ref=badge)

DeepSeek Harness प्लगइन के लिए सामुदायिक प्रमाणन: एक सार्वजनिक विनिर्देश, मशीन से जाँचा जा सकने वाला स्कोरिंग मॉडल और बैज वाली स्वतंत्र रजिस्ट्री। यह प्रोजेक्ट इसलिए है क्योंकि पारिस्थितिकी तंत्र की कैनोनिकल सूची स्पष्ट रूप से कहती है कि वह "प्लगइन गुणवत्ता की निर्णायक नहीं है" और "सूची में होना सुरक्षा समीक्षा नहीं है" — सूचियों से बाहर किसी को यह जाँच चलानी चाहिए, दोबारा पैदा करने योग्य तरीके से, और उन पर अधिकार का दावा किए बिना।

**यह एक डेटा रिपॉज़िटरी है, प्लगइन नहीं।** इसमें कोई Cordis प्लगइन नहीं आता, यहाँ कुछ भी इंस्टॉल करने योग्य नहीं है और npm पर कुछ भी प्रकाशित नहीं होता: `package.json` `private` है और केवल इसलिए मौजूद है ताकि पारिस्थितिकी के टूल मेटाडेटा पढ़ सकें (`dsh.manifestVersion`, `engines.dsh` — वही तीन-खंड वाली रेंज जिसे `dsh-plugin-kit/data/peer-range.json` कैनोनिकल के रूप में दर्ज करता है, फिर से लिखे जाने के बजाय जस का तस उद्धृत)।

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dsh-certification-flow-v5-dark.svg">
  <img src="docs/assets/dsh-certification-flow-v5-light.svg" width="100%" alt="dsh-certification का संरचना चित्र: पाँच आयाम A–E (manifest, build hygiene, supply chain, release integrity, install smoke) data/attestations.json में जाते हैं; scripts/check-freshness.mjs 30 दिन की ताज़गी, dsh-cert-mcp मिरर का sha256 और README मार्कर जाँचता है; scripts/badge.mjs badges/<owner>__<repo>.svg बनाता है और .github/workflows/registry.yml उन्हें प्रतिदिन फिर से रेंडर करता है">
</picture>

*संरचना: पाँच आयाम → data/attestations.json → check-freshness गेट → badges। चित्र के लेबल असली स्क्रिप्ट नाम, पथ और workflow फ़ाइलें हैं, इसलिए एक ही चित्र पाँचों भाषाओं के लिए है।*

## पाँच आयाम

![dsh-certification: node scripts/check-freshness.mjs का असली टर्मिनल रन — freshness ok (सबसे पुराना generatedAt 2026-11-01, 20 दिन, सीमा 30), mirror ok (sha256 bab74bc817b1b511… समान), roster-count 98 ok, attested-count 1 ok और baseline-batch वाक्य 98/87/8/3/1 ok, अंत में check-freshness: ok](docs/assets/dsh-certification-demo.gif)

*एनिमेशन: 2026-11-21 को DSH_KIT_ROSTER को dsh-kit roster checkout पर इंगित करते हुए node scripts/check-freshness.mjs का दोबारा रन — असली stdout, कोई नेटवर्क कॉल नहीं।*

| आयाम | साक्ष्य स्रोत | जाँच |
|---|---|---|
| A. Manifest अनुपालन | स्थिर | `dsh.bundle` manifest, LICENSE (SPDX), keywords/topics का मिलान, पाँच भाषाओं की README, engines |
| B. Build hygiene | स्थिर | `files` allowlist की पूर्णता, dependency घोषणाएँ (peer/optional), `postinstall` में दुर्भावनापूर्ण पैटर्न नहीं, lint/typecheck गेट मौजूद |
| C. Supply chain | OpenSSF Scorecard | 18 upstream जाँचें, आधिकारिक Scorecard API और badges के ज़रिए |
| D. Release integrity | npm provenance | SLSA अटेस्टेशन, `npm audit signatures` से सत्यापन योग्य |
| E. Install smoke | dsh-test-drive रिकॉर्ड | अलग-थलग अस्थायी (throwaway) प्रोफ़ाइल में असली install/load/keyless boot (चार स्थितियाँ: `ok` / `load-fail` / `install-fail` / `skip`) |

## ग्रेड

- **A** — पाँचों पास (E का `ok` होना ज़रूरी), कोई वीटो नहीं
- **B** — E पास और A/B/C/D में से कम से कम तीन पास
- **C** — E पास, बाकी अधूरे
- **D** — कोई भी कठोर गेट विफल (`dsh.bundle` गायब, लाइसेंस नहीं, दुर्भावनापूर्ण पैटर्न मिला)
- **सुरक्षा वीटो** — obfuscated कोड, credential exfiltration या install के समय अप्रत्याशित व्यवहार तुरंत D देते हैं, और कारण प्रकाशित होता है
- **पर्यावरण से अवरुद्ध E** — जब E केवल किसी अनुपस्थित-पर्यावरण गेट की वजह से `install-fail` पर समाप्त होता है (जैसे pnpm का इंटरैक्टिव `approve-builds`, जिसकी पुष्टि sandbox में नहीं हो सकती), तो प्रविष्टि **B** ग्रेड बनाए रखती है (यदि A–D पास हों) और reproduction कमांड के साथ `environment-blocked` दर्ज करती है। पर्यावरणीय गेट कभी `D` के रूप में दर्ज नहीं होते।

## साक्ष्य अनुशासन (मूलभूत)

हर स्कोर वास्तविक, दोबारा चलाए जा सकने वाले निष्पादन से आना चाहिए — प्रोब परिणाम, Scorecard रन, provenance जाँच या test-drive रिकॉर्ड। साक्ष्य न होना `no-evidence` है, कभी अनुमान नहीं। हर आयाम snapshot तिथि के साथ ऑडिट लिंक दर्ज करता है।

## रजिस्ट्री और बैज

- `data/attestations.json` — रजिस्ट्री: हर अटेस्टेड रिपॉज़िटरी के लिए एक प्रविष्टि (आयाम-वार परिणाम + साक्ष्य लिंक + विनिर्देश संस्करण)। **CI रोज़ाना cron पर इसी से बैज फिर से रेंडर करता है (`.github/workflows/registry.yml`); रजिस्ट्री की प्रविष्टियाँ मानव-क्यूरेशन हैं, CI का आउटपुट नहीं।**
- बैज आर्टिफ़ैक्ट `badges/` में कमिट किए गए SVG हैं, जिन्हें `scripts/badge.mjs` रजिस्ट्री से बनाता है (रजिस्ट्री ही एकमात्र स्रोत है; स्क्रिप्ट कभी ग्रेड नहीं गढ़ती)।
- **काम करने वाला बैज URL** (आर्टिफ़ैक्ट फ़ाइलनाम में **डबल अंडरस्कोर** `<owner>__<repo>`):

  ```
  https://raw.githubusercontent.com/PerryLink/dsh-certification/main/badges/<owner>__<repo>.svg
  ```

  चिपकाने योग्य:

  ```markdown
  [![dsh-attested](https://raw.githubusercontent.com/PerryLink/dsh-certification/main/badges/<owner>__<repo>.svg)](https://github.com/PerryLink/dsh-certification#registry-and-badges)
  ```

- ⚠️ `https://perrylink.github.io/dsh-certification/badge/<owner>/<repo>.svg` रूप v0.2 से पहले दर्ज था लेकिन **404 लौटाता था**: इस रिपॉज़िटरी का कभी GitHub Pages deployment नहीं हुआ (`registry.yml` केवल SVG रेंडर और कमिट करता है)। जब तक Pages deploy वाकई मौजूद न हो, वह URL प्रचारित न करें; ऊपर वाला raw रूप इस्तेमाल करें, या Pages सक्षम करके `badges/<owner>__<repo>.svg` को `badge/<owner>/<repo>.svg` पर प्रकाशित करें।
- ग्रेड: A / B / C / D, और रजिस्ट्री में न होने वाली किसी भी चीज़ के लिए धूसर `no-data`।
- सूचियाँ और marketplaces केवल बैज लिंक करते हैं — उनका "कोई समर्थन नहीं" रुख अपरिवर्तित रहता है।

## जाँच

`node scripts/check-freshness.mjs` इस रिपॉज़िटरी का गेट है (केवल `node:` बिल्ट-इन, शून्य निर्भरताएँ) और हर push पर, हर pull request पर और साप्ताहिक रूप से चलता है:

- **freshness** — रजिस्ट्री का `generatedAt` और हर प्रविष्टि का `snapshot` अधिकतम 30 दिन पुराना होना चाहिए। पुराना प्रमाणन साक्ष्य build को विफल करता है, बजाय चुपचाप किसी प्लगइन को वैसा बताने के जैसा वह एक महीने पहले था।
- **mirror** — `dsh-cert-mcp` द्वारा परोसी जाने वाली केवल-पठन प्रति में वही रजिस्ट्री सामग्री होनी चाहिए जो `data/attestations.json` में है, ताकि MCP का लाइव URL कभी ऐसे ग्रेड न परोसे जिनके पीछे यह रिपॉज़िटरी अब नहीं खड़ी है। तुलना LF-सामान्यीकृत टेक्स्ट पर होती है, क्योंकि दोनों रिपॉज़िटरी अपने-अपने `core.autocrlf` वाले अलग clones हैं और कच्चे bytes की तुलना ऐसा अंतर बताती जो वास्तव में नहीं है। जब sibling रिपॉज़िटरी मौजूद न हो, जाँच `SKIP` बताती है — स्पष्ट "कुछ भी तुलना नहीं हुई", कभी पास नहीं।
- **markers** — ऊपर के दो roadmap आँकड़े अपने स्रोतों के विरुद्ध जाँचे जाते हैं, ताकि prose roster से अलग न भटके।

`DSH_CERT_MIRROR` और `DSH_KIT_ROSTER` sibling पथों को ओवरराइड करते हैं। `.github/workflows/checks.yml` (केवल-पठन, `contents: read`) `dsh-cert-mcp` और `dsh-plugin-kit` को `_siblings/` में checkout करके इन switches से पास करता है, इसलिए तीनों assertions हर push और pull request पर साप्ताहिक के अतिरिक्त चलती हैं; और वही switches स्थानीय रूप से staleness, drift, खराब JSON और अनुपस्थित-स्रोत के मामले आज़माने का तरीका हैं। निर्धारित `registry.yml` (जिसे badges कमिट करने के लिए `contents: write` चाहिए) केवल freshness assertion चलाता है, क्योंकि वह job और कुछ checkout नहीं करता।

## मौजूदा टूल से संबंध

- `dsh-test-drive` आयाम E के रिकॉर्ड देता है (पहले से खुला `test_drive` domain)
- `dsh-score` प्रमाणन रिकॉर्ड को install साक्ष्य के रूप में उपभोग करता है
- `dsh-skill-pack-security` आयाम B के लिए दुर्भावनापूर्ण-पैटर्न जाँच देता है
- OpenSSF Scorecard पूरा आयाम C देता है — यह प्रोजेक्ट उसे फिर से नहीं बनाता
- `dsh-plugin-scorecard`, `dsh-plugin-audit` आदि से ओवरलैप: governance चर्चा [issue #1](../../issues/1) में खुली है; लक्ष्य एक सहमत मानक है, इक्कीसवाँ स्कोरर नहीं

## DSH स्तरों से संबंध

DSH adoption स्तर इस रिपॉज़िटरी से बाहर, निजी `dsh-standards` प्रोजेक्ट द्वारा परिभाषित होते हैं (लिखते समय यह एक draft था; एक्सेस के लिए maintainer से पूछें)। सार्वजनिक शिक्षण-प्रति [dsh-plugin-guide/guide/standards.md](https://github.com/PerryLink/dsh-plugin-guide/blob/main/guide/standards.md) में रखी जाती है: **DSH-ENTRY** (compatibility baseline), **DSH-FLOW** (recommended adoption) और **DSH-BOOST** (guiding practice)। ये इस रिपॉज़िटरी के ग्रेड नहीं हैं, और यहाँ कुछ भी उन्हें प्रमाणित नहीं करता।

- **ग्रेड बनाम स्तर।** `A`/`B`/`C`/`D` ऊपर की पाँच जाँचों पर गुणवत्ता और साक्ष्य का ग्रेड है; `ENTRY`/`FLOW`/`BOOST` adoption की गहराई है। दोनों अक्ष लंबवत हैं — कोई प्लगइन ग्रेड `A` हो सकता है और फिर भी `FLOW = not-run` हो सकता है।
- **ENTRY.** आयाम A (manifest) और E (install smoke) — साथ में स्पष्ट सुरक्षा वीटो, जो इस रिपॉज़िटरी की अपनी पूर्व-शर्त है, ENTRY की माँग नहीं — ज़रूरी हैं पर ENTRY `runtime` के लिए **पर्याप्त नहीं**। मानकों के G0–G5 गेट ही वह तय करते हैं, और यह रजिस्ट्री कोई गेट परिणाम दर्ज नहीं करती।
- **FLOW और BOOST.** यहाँ का कोई आयाम J1–J8 यात्राएँ नहीं चलाता और न ही LEV-1..LEV-8 प्रथाएँ मापता है; दोनों [`data/standards-crosswalk.json`](./data/standards-crosswalk.json) में `not-covered` दर्ज हैं।

पूरा crosswalk — हर आयाम क्या सिद्ध करता है, किस गेट को खिला सकता है, और यह रिपॉज़िटरी क्या दावा नहीं करेगी — [`STANDARDS.md`](./STANDARDS.md) में है।

## रोडमैप

1. विनिर्देश v1 प्रकाशित करें (यह दस्तावेज़)
2. रजिस्ट्री + बैज CI
> **2026-11-01 अपडेट:** roster अब `dsh-plugin-kit/data/repos.json` से **98** गैर-infra रिपॉज़िटरी निकालता है — **87 active / 8 frozen / 3 retired** (99 प्रविष्टियाँ, जिनमें kit की infra पंक्ति शामिल है)। नीचे के आँकड़े उसी स्रोत के अनुसार अपडेट हैं; पुरानी 94 / 86 / 5 / 3 पंक्ति अक्टूबर-पूर्व बैच की baseline थी।

3. PerryLink प्लगइन परिवार को पहले baseline बैच के रूप में अटेस्ट करें: परिवार के roster में **98** प्लगइन रिपॉज़िटरी हैं — **87** सक्रिय रूप से अनुरक्षित, **8** फ़्रोज़न और **3** सेवानिवृत्त, यानी हर वह प्रविष्टि जिसका `role` `infra` नहीं है — और अब तक **1** अटेस्टेड है। दोनों आँकड़े व्युत्पन्न हैं, हाथ से लिखे नहीं — `scripts/check-freshness.mjs` उन्हें और इस वाक्य के bold आँकड़ों को `dsh-plugin-kit/data/repos.json` तथा इस रजिस्ट्री के विरुद्ध जाँचता है। <!-- roster-count: 98 --><!-- attested-count: 1 -->
   - ⚠️ **इन 98 का दायरा, ताकि इसे परिवार का आकार न पढ़ा जाए** — आधिकारिक तालिका [`dsh-kit` की README, "Family counts — one definition per number"](https://github.com/PerryLink/dsh-kit#family-counts--one-definition-per-number) में है। यह संख्या मशीन-पठनीय roster फ़ाइल है: वहाँ 99 प्रविष्टियाँ = ये 98 प्लस `dsh-plugin-kit` की infra प्रविष्टि। यह **नहीं** है 45-पंक्ति वाली family तालिकाएँ (जो सेवानिवृत्त पंक्तियाँ रखती हैं और कभी कोई नहीं हटातीं) और **नहीं** है install-spec सूची (जो सेवानिवृत्त roster सदस्यों को बाहर रखती है)।
4. अन्य शीर्ष प्लगइन लेखकों के साथ पायलट करें
5. कैनोनिकल सूची और marketplaces को badge प्रदर्शन प्रस्तावित करें (केवल लिंक, कोई समर्थन नहीं)

**DSH परिवार लाइन:** यह रिपॉज़िटरी **PerryLink DeepSeek Harness प्लगइन परिवार** की सदस्य है, जिसकी वर्तमान लाइन `dsh-v0.2.2-alpha.3` है।

## PerryLink DSH Plugin Family

यह प्रोजेक्ट [PerryLink](https://github.com/PerryLink) की **88 सक्रिय रूप से अनुरक्षित** DeepSeek Harness प्रविष्टियों में से एक है (87 प्लगइन + kit की infra पंक्ति)। परिवार के roster में कुल **99** प्रविष्टियाँ हैं — **8 फ़्रोज़न**, **3 सेवानिवृत्त** — और यह नीचे सिकुड़ा रहता है ताकि हर README की पहली स्क्रीन छोटी रहे। हर पंक्ति देखने के लिए इसे खोलें, कारण Status कॉलम में है।

<details>
<summary>सभी प्लगइन खोलें</summary>

| प्लगइन | संक्षिप्त विवरण | स्थिति |
|---|---|---|
| **[dsh-aqua-input-check](https://github.com/PerryLink/dsh-aqua-input-check)** | 水产养殖投入品记录核对（按投入品用量、休药期与起捕间隔核对记录自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-archive-check](https://github.com/PerryLink/dsh-archive-check)** | 档案归档完整性与保管期限核对（依据《档案法》及配套规定，仅提示差异，不作出定性结论） |  |
| **[dsh-auto-review](https://github.com/PerryLink/dsh-auto-review)** | Second-model auto-review on the approval chain, fail-closed by default |  |
| **[dsh-bid-ca-precheck](https://github.com/PerryLink/dsh-bid-ca-precheck)** | 投标文件符合性预检（按招标文件逐条核对响应情况，仅提示差异，不作出定性结论） |  |
| **[dsh-bid-qual-check](https://github.com/PerryLink/dsh-bid-qual-check)** | 投标人资格条件核对（按资格条件与证明材料核对逐条响应，仅提示差异，不作出定性结论） |  |
| **[dsh-check-pack](https://github.com/PerryLink/dsh-check-pack)** | 给合规/审计/质量：一条命令装齐 53 个 PerryLink 合规核查器（Check Pack），覆盖报关、招投标、EHS、医疗、法律、财务等场景。 One-command installer for the 53-checker PerryLink compliance suite. |  |
| **[dsh-checkpoint-rewind](https://github.com/PerryLink/dsh-checkpoint-rewind)** | Claude Code /rewind-equivalent: snapshots, session forks, one-shot restore |  |
| **[dsh-click](https://github.com/PerryLink/dsh-click)** | Cross-platform native desktop control for DeepSeek Harness — Windows first. |  |
| **[dsh-clinic](https://github.com/PerryLink/dsh-clinic)** | Zero-dependency static + sandbox smoke detector for DeepSeek Harness plugins: package-structure gates (R), cordis contract scans (K), keyless-headless sandbox smoke (D), and ecosystem-listing checks (CC). |  |
| **[dsh-codemod](https://github.com/PerryLink/dsh-codemod)** | Plugin-author upgrade skill for DeepSeek Harness, new-generation single package: one corridor index reads the caller peer band and routes it to the matching closed corridor card (dsh 0.1.3-alpha.1 -> 0.1.5-rc.1 and 0.1.5-rc.2 -> 0.1.6-alpha.2), with a zero-dependency seam scanner shipped as a bundle skill and an npx CLI. |  |
| **[dsh-composer-history](https://github.com/PerryLink/dsh-composer-history)** | Terminal-style input history for the web composer: arrows, Ctrl+R search |  |
| **[dsh-contract-stance](https://github.com/PerryLink/dsh-contract-stance)** | 合同条款立场台账核对（按立场、风险等级与退让底线核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-customs-doc-check](https://github.com/PerryLink/dsh-customs-doc-check)** | 报关单证一致性核对（按公开的报关单填制规范核对单证间字段一致性，仅提示差异，不作出定性结论） |  |
| **[dsh-data-quality](https://github.com/PerryLink/dsh-data-quality)** | Dataset quality checks and citation cross-checks (the optional numeric bridge consumed here) |  |
| **[dsh-demurrage-ledger](https://github.com/PerryLink/dsh-demurrage-ledger)** | 滞箱费台账核对（按免箱期与费率核对超期天数与金额的自洽性，仅提示差异，不作出定性结论） |  |
| **[dsh-dida](https://github.com/PerryLink/dsh-dida)** | TickTick (Dida365) task bridge for DeepSeek Harness: a session-header task panel (list filter, quick add, complete, delete, due dates, drag reorder), eleven curated ticktick_* tools, and a plugin settings card over the official TickTick MCP endpoint. |  |
| **[dsh-doublecheck](https://github.com/PerryLink/dsh-doublecheck)** | Engineering-discipline guard: requirements grill, test gates, adversary review |  |
| **[dsh-drill-script-check](https://github.com/PerryLink/dsh-drill-script-check)** | 应急演练脚本核对（按阶段、响应分级、指挥与资源要素核对脚本齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-eia-guide-check](https://github.com/PerryLink/dsh-eia-guide-check)** | 环评导则适用性核对（按要素、导则名称与执行标准核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-emergency-plan](https://github.com/PerryLink/dsh-emergency-plan)** | 应急预案要素齐备性与条款级页码定位（依据 GB/T 29639-2020 等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-essay-rubric-check](https://github.com/PerryLink/dsh-essay-rubric-check)** | 作文量表评分核对（按评分项、原文定位与分值算术核对评分自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-evidence-check](https://github.com/PerryLink/dsh-evidence-check)** | 证据清单齐备性核对（按待证事实与证据要素核对清单自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-export-ctl-check](https://github.com/PerryLink/dsh-export-ctl-check)** | 出口管制物项核对（按管制清单、最终用途与许可证核对台账齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-fast](https://github.com/PerryLink/dsh-fast)** | Read-only performance diagnostics for DeepSeek Harness. |  |
| **[dsh-fmea-table-check](https://github.com/PerryLink/dsh-fmea-table-check)** | FMEA 分析表要素齐备性与风险顺序数一致性核对（按公开的 FMEA 方法标准核对表内自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-forecast-penalty](https://github.com/PerryLink/dsh-forecast-penalty)** | 预测准确率考核核对（按准确率、门槛与考核单价核对考核费用的算术自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-fund-research](https://github.com/PerryLink/dsh-fund-research)** | Deterministic research reports for Chinese public mutual funds |  |
| **[dsh-github-bot](https://github.com/PerryLink/dsh-github-bot)** | GitHub for DeepSeek Harness: PR/issue/repo/file tools, reviews, and CI checks — every write gated by human approval, token never logged. |  |
| **[dsh-gongwen-flow-check](https://github.com/PerryLink/dsh-gongwen-flow-check)** | 公文流转与办理时限核对（按公开的公文处理规范核对环节完整性与时限，仅提示差异，不作出定性结论） |  |
| **[dsh-gongwen-word-check](https://github.com/PerryLink/dsh-gongwen-word-check)** | 公文格式要素核对（按公开的党政机关公文格式标准核对版头与主体要素，仅提示差异，不作出定性结论） |  |
| **[dsh-guard-plan-qc](https://github.com/PerryLink/dsh-guard-plan-qc)** | 劳动保护与安全防护用品配置表核对（按公开的配备标准核对品名规格与配置数量，仅提示差异，不作出定性结论） |  |
| **[dsh-hazchem-check](https://github.com/PerryLink/dsh-hazchem-check)** | 危险化学品临界量与重大危险源辨识核对（按储存量与临界量核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-hazplan-check](https://github.com/PerryLink/dsh-hazplan-check)** | HAZOP 分析表要素齐备性与偏差推导一致性核对（依据公开的危险与可操作性分析标准，仅提示差异，不作出定性结论） |  |
| **[dsh-hidden-risk-map](https://github.com/PerryLink/dsh-hidden-risk-map)** | 安全隐患台账→《工贸企业重大事故隐患判定标准》（应急管理部令第10号）条款映射提示（仅提示差异，不作出定性结论） |  |
| **[dsh-hs-classify](https://github.com/PerryLink/dsh-hs-classify)** | 商品归类层级一致性核对（按章、品目、子目与商品编号的层级关系核对自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-icd-rule-check](https://github.com/PerryLink/dsh-icd-rule-check)** | ICD 合并编码与星剑号配对提示（依据 GB/T 14396-2016 与公开的 ICD 使用说明，仅提示差异，不作出定性结论） |  |
| **[dsh-industry-research](https://github.com/PerryLink/dsh-industry-research)** | Industry research orchestration that seals its deliverables through this plugin's `ctx.researchReport.assemble` |  |
| **[dsh-lawcite-adapter](https://github.com/PerryLink/dsh-lawcite-adapter)** | 法条引用与文书要素核对（按名称、条号、原文与效力状态核对引用可核验性，仅提示差异，不作出定性结论） |  |
| **[dsh-laya](https://github.com/PerryLink/dsh-laya)** | Laya typed decisions (`noul`/`choice`/`score`) as a first-class Cordis service and model-visible tools |  |
| **[dsh-lc-doc-check](https://github.com/PerryLink/dsh-lc-doc-check)** | 信用证单据条款核对（按单据种类、份数、期限与不符点核对交单自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-learning-gap-check](https://github.com/PerryLink/dsh-learning-gap-check)** | 能力差距与培养计划核对（按能力等级差、培养措施与完成期限核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-library](https://github.com/PerryLink/dsh-library)** | Local document knowledge base for DeepSeek Harness. |  |
| **[dsh-local-ai](https://github.com/PerryLink/dsh-local-ai)** | Local-model (Ollama) integration for DeepSeek Harness. |  |
| **[dsh-lsp-actions](https://github.com/PerryLink/dsh-lsp-actions)** | LSP diagnostics, formatting, completion, code actions and rename over language servers |  |
| **[dsh-mask](https://github.com/PerryLink/dsh-mask)** | PII masking middleware: anonymize at the model boundary, restore at the display layer |  |
| **[dsh-mcp-panel](https://github.com/PerryLink/dsh-mcp-panel)** | Read-only MCP runtime panel: /mcp command + Settings tab with status, tools and errors |  |
| **[dsh-medrec-qc](https://github.com/PerryLink/dsh-medrec-qc)** | 病案首页形式质控与逻辑矛盾提示（依据国卫办医发〔2016〕24号等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-memento](https://github.com/PerryLink/dsh-memento)** | Approval-gated cross-session memory: ctx.memory seam + SQLite + memory tool |  |
| **[dsh-nsfc-form-check](https://github.com/PerryLink/dsh-nsfc-form-check)** | 国家自然科学基金申请书形式审查与匿名化提示（依据《国家自然科学基金条例》与申请人导入的当年指南，仅提示差异，不作出定性结论） |  |
| **[dsh-nurse-record-check](https://github.com/PerryLink/dsh-nurse-record-check)** | 护理记录时限、签名与评估表完整性校验（依据《病历书写基本规范》等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-observe](https://github.com/PerryLink/dsh-observe)** | OpenTelemetry and Langfuse observability exporter for DeepSeek Harness. |  |
| **[dsh-origin-rvc-check](https://github.com/PerryLink/dsh-origin-rvc-check)** | 原产地区域价值成分核对（按 RVC 定义式与门槛核对原产地判定自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-ota-review-check](https://github.com/PerryLink/dsh-ota-review-check)** | 在线点评回复台账核对（按回复时限、问题分类与整改状态核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-output-styles](https://github.com/PerryLink/dsh-output-styles)** | Claude Code outputStyles-equivalent runtime style switching |  |
| **[dsh-paper-doc-adapter](https://github.com/PerryLink/dsh-paper-doc-adapter)** | 试卷解析结构核对（按题型、答案、分值与 yotta 标识核对试卷结构自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-param-repair](https://github.com/PerryLink/dsh-param-repair)** | Map request parameters across 11 vendor dialects and repair malformed JSON tool output with deterministic, auditable rules. No network, no model call, no invented data. |  |
| **[dsh-permission-rules](https://github.com/PerryLink/dsh-permission-rules)** | Claude Code-style declarative allow/deny/ask permission rules with audit |  |
| **[dsh-permit-report-check](https://github.com/PerryLink/dsh-permit-report-check)** | 行政许可办理核对（按申请材料、法定时限与决定记录核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-phocinae](https://github.com/PerryLink/dsh-phocinae)** | Local non-generative decision layer for DeepSeek Harness: phocinae_ask / phocinae_gate tools plus a fail-closed approval gate |  |
| **[dsh-phone-inbox](https://github.com/PerryLink/dsh-phone-inbox)** | The session's inbound phone (WeChat bridge) attachment inbox: list the attachments this conversation actually received and adopt one into the workspace on demand. |  |
| **[dsh-pipeline-check](https://github.com/PerryLink/dsh-pipeline-check)** | 项目全流程文件链条核对（按环节先后与金额口径核对链条自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-pleading-draft](https://github.com/PerryLink/dsh-pleading-draft)** | 起诉状要素齐备性核对（按诉请、事实、证据、法条四要素核对文书齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-policy-brief-draft](https://github.com/PerryLink/dsh-policy-brief-draft)** | 政策专报要素核对（按观点、依据、出处与建议核对专报齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-power-loss-split](https://github.com/PerryLink/dsh-power-loss-split)** | 线损电量拆分与考核表核对（依据公开的线损计算口径，仅提示差异，不作出定性结论） |  |
| **[dsh-power-ticket-check](https://github.com/PerryLink/dsh-power-ticket-check)** | 电力工作票要素齐备性与签发许可顺序核对（依据现行电力安全工作规程，仅提示差异，不作出定性结论） |  |
| **[dsh-ppap-check](https://github.com/PerryLink/dsh-ppap-check)** | PPAP 提交要素齐备性核对（按公开的 PPAP 手册与提交等级核对要素齐备与版本填写，仅提示差异，不作出定性结论） |  |
| **[dsh-primer](https://github.com/PerryLink/dsh-primer)** | The DSH plugin development guide as an installable skill: packaging, contracts, checks, and a pack verifier for builds. |  |
| **[dsh-protest-deadline](https://github.com/PerryLink/dsh-protest-deadline)** | 质疑与投诉期限核对（按公开的政府采购质疑投诉规定核对期限与台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-railway-window](https://github.com/PerryLink/dsh-railway-window)** | 铁路施工天窗台账核对（按时间窗、时长算术与命令号核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-repair-order-qc](https://github.com/PerryLink/dsh-repair-order-qc)** | 维修工单结算核对（按项目金额、配件工时合计与结算总额核对算术自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-research-report](https://github.com/PerryLink/dsh-research-report)** | Verifiable research-report engine: content-addressed evidence ledger and sealed versions |  |
| **[dsh-review-reply-check](https://github.com/PerryLink/dsh-review-reply-check)** | 审稿意见逐条回应覆盖核对（按意见与回应一一对应核对覆盖完整性，仅提示差异，不作出定性结论） |  |
| **[dsh-rulefile-check](https://github.com/PerryLink/dsh-rulefile-check)** | 规则库文件自检核对（按条款引用纪律与效力上限核对规则库自身合规，仅提示差异，不作出定性结论） |  |
| **[dsh-safety-brief-check](https://github.com/PerryLink/dsh-safety-brief-check)** | 安全技术交底记录要素齐备性与签字闭环核对（依据公开的安全生产法规与检查标准，仅提示差异，不作出定性结论） |  |
| **[dsh-scaffold](https://github.com/PerryLink/dsh-scaffold)** | Shared zero-runtime-dependency toolkit for PerryLink DSH plugins: a pluggable Provider registry seam, fail-closed approval and adaptive session-event gates, mechanical verify scripts, shared sanitize/pricing/judge modules, and a new-plugin skeleton. |  |
| **[dsh-score](https://github.com/PerryLink/dsh-score)** | Multi-dimensional quality scoring for DeepSeek Harness plugins. |  |
| **[dsh-security-pack](https://github.com/PerryLink/dsh-security-pack)** | Security skill-pack provider: pre-packaged security review skills with a risk card; ships both host and client faces. |  |
| **[dsh-session-sync](https://github.com/PerryLink/dsh-session-sync)** | Cross-device session sync for DeepSeek Harness — a dedicated git mirror of your session store. |  |
| **[dsh-site-log-check](https://github.com/PerryLink/dsh-site-log-check)** | 监理日志跨日连续性与旁站覆盖提示（依据 GB/T 50319-2013 等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-soe-decision-check](https://github.com/PerryLink/dsh-soe-decision-check)** | 三重一大决策台账核对（按前置研究、会议审议与决议记录核对程序留痕，仅提示差异，不作出定性结论） |  |
| **[dsh-soilwater-check](https://github.com/PerryLink/dsh-soilwater-check)** | 土壤与地下水监测台账核对（按点位、监测项目、限值与超标判定核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-sop-sync-check](https://github.com/PerryLink/dsh-sop-sync-check)** | 作业规程与 FMEA／控制计划一致性核对（按编号与版本核对三份文件是否同步，仅提示差异，不作出定性结论） |  |
| **[dsh-spc-gbt-adapter](https://github.com/PerryLink/dsh-spc-gbt-adapter)** | 控制图常数与过程能力指数核对（按 GB/T 4091 的常规控制图常数表核对控制限与 Cp/Cpk 的自洽性，仅提示差异，不作出定性结论） |  |
| **[dsh-talk](https://github.com/PerryLink/dsh-talk)** | Voice-first session loop for DeepSeek Harness: talk to it, hear it answer. |  |
| **[dsh-tender-extract](https://github.com/PerryLink/dsh-tender-extract)** | 招标文件条款摘录核对（按出处与原文逐字核对摘录完整性，仅提示差异，不作出定性结论） |  |
| **[dsh-tender-matrix](https://github.com/PerryLink/dsh-tender-matrix)** | 评标因素评分矩阵核对（按分值上限与合计核对矩阵自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-test-drive](https://github.com/PerryLink/dsh-test-drive)** | Isolated install-and-smoke test drives for DeepSeek Harness plugins. |  |
| **[dsh-warranty-calc](https://github.com/PerryLink/dsh-warranty-calc)** | 质保期与索赔金额核对（按质保期月数与里程、索赔金额算式核对自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-autotier](https://github.com/PerryLink/dsh-autotier)** | Automatic strong/cheap model-tier routing with deterministic risk guards and a `/tier` command | 🧊 फ़्रोज़न — रिपॉज़िटरी README देखें |
| **[dsh-budget](https://github.com/PerryLink/dsh-budget)** | Cost governance for DeepSeek Harness: budgets, carbon, and latency in one panel. | 🧊 फ़्रोज़न — रिपॉज़िटरी README देखें |
| **[dsh-cert-mcp](https://github.com/PerryLink/dsh-cert-mcp)** | Read-only MCP server exposing the certification registry: grades, snapshots and five-dimension evidence | 🧊 फ़्रोज़न — रिपॉज़िटरी README देखें |
| **[dsh-claude-move](https://github.com/PerryLink/dsh-claude-move)** | Migrate Claude Code sessions, memory, skills and CLAUDE.md into DSH | 🧊 फ़्रोज़न — रिपॉज़िटरी README देखें |
| **[dsh-community-bench](https://github.com/PerryLink/dsh-community-bench)** | In-app Community Bench tab for DeepSeek Harness: reads the public, task-conditioned registry and shows the same score everyone sees; no local scoring. | 🧊 फ़्रोज़न — रिपॉज़िटरी README देखें |
| **[dsh-defend](https://github.com/PerryLink/dsh-defend)** | Prompt-injection, jailbreak, and secret-leak defense for DeepSeek Harness. | 🧊 फ़्रोज़न — रिपॉज़िटरी README देखें |
| **[dsh-draw](https://github.com/PerryLink/dsh-draw)** | Unified static-image generation routing for DeepSeek Harness. | 🧊 फ़्रोज़न — रिपॉज़िटरी README देखें |
| **[dsh-reach](https://github.com/PerryLink/dsh-reach)** | Multi-channel approval/question bridge: WeChat/Telegram/Feishu, session console | 🧊 फ़्रोज़न — रिपॉज़िटरी README देखें |
| **[dsh-background-agents](https://github.com/PerryLink/dsh-background-agents)** | Durable background child agents with a Web UI sidebar, messaging and interrupt | 🚫 सेवानिवृत्त — रिपॉज़िटरी README देखें |
| **[dsh-session-pin](https://github.com/PerryLink/dsh-session-pin)** | Pin sessions in the Web sidebar with durable ordering | 🚫 सेवानिवृत्त — रिपॉज़िटरी README देखें |
| **[dsh-team-rooms](https://github.com/PerryLink/dsh-team-rooms)** | Cross-session team rooms: shared message bus, task board and timeline | 🚫 सेवानिवृत्त — रिपॉज़िटरी README देखें |

</details>

</details>

</details>

## लाइसेंस

MIT। देखें [LICENSE](./LICENSE)।


## HCB डेटा

[HCB](https://github.com/PerryLink/dsh-hcb) 0.12.0 — **136 checked + 7 tested / 3 FLOW `manual` / gate 2 blocked · 31 review · 109 pilot · 1 `production-candidate`** — checker **`dsh-standards@1.7.0` पर 143/143 पंक्तियाँ** — घोषित compatibility / tracks / risk का प्रामाणिक स्रोत है। 7 tested पंक्तियाँ हैं `dsh-plugin-guide@0.5.9` (एकमात्र production-candidate) और साथ में `dsh-budget@0.4.26`, `dsh-defend@0.3.28`, `dsh-mask@0.2.25`, `dsh-memento@0.5.31`, `dsh-observe@0.2.28`, `dsh-test-drive@0.3.26`; बाकी 136 पंक्तियाँ tested नहीं हैं, इसलिए इससे roster production-ready नहीं हो जाता। रजिस्ट्री HCB पंक्तियाँ लिंक कर सकती है; **प्रमाणन A–D और HCB गेट लंबवत हैं**।
