---
name: enzo-paper-info
description: Use when the user provides a paper title, paper link, arXiv/OpenReview/CVF/IEEE/ACM page, or method name and asks for basic paper information, metadata, official paper address, publication date, team, venue, source code address, official GitHub repository, or clone/download command.
---

# Paper Info

Answer in Chinese unless the user asks otherwise.

## Trigger

Use this skill when the user provides a paper title, paper URL, or method name and asks for "基本信息", "论文信息", "源码地址", "GitHub 地址", "代码地址", "下载命令", "发表时间", "会议/期刊", "作者/团队", or a close variant.

## Workflow

1. Look up reliable sources for the paper.
   - Prefer the official paper page, arXiv, OpenReview, CVF, AAAI/IJCAI/NeurIPS/ICML/ICLR proceedings, IEEE, ACM, Springer, project page, and official GitHub repository.
   - Verify venue using this priority: official conference/journal proceedings page; OpenReview, CVF, IEEE, ACM, Springer, or PMLR page; project page or official repository citation; arXiv comments field.
   - Do not decide "未找到可靠的会议 / 期刊录用信息" from arXiv alone. Do not conclude "arXiv only" until exact-title searches across likely venue sources have failed.
   - For computer vision papers, especially if the title/method is well-known, explicitly search CVF Open Access by exact title and first author before reporting no venue. Try both web search and the likely CVF URL pattern, for example `site:openaccess.thecvf.com/content "Exact Paper Title" "FirstAuthor"` and `https://openaccess.thecvf.com/content/<VENUEYEAR>/html/<FirstAuthor>_<Title_With_Underscores>_<VENUE>_<YEAR>_paper.html`.
   - Treat a verified proceedings page as stronger venue evidence than an older arXiv page or an official repository README/BibTeX that still cites only arXiv. Example: `Segment Anything` has arXiv `2304.02643`, but CVF Open Access verifies it as `ICCV 2023`; report `ICCV 2023`.
   - If broad search does not find a venue but the paper is likely from CV/ML, search exact-title variants with punctuation removed, title words joined by underscores, first-author surname, and venue/year candidates before concluding no reliable venue.
   - If multiple papers have similar names, disambiguate by authors, year, venue, or the user-provided link. If ambiguity remains, ask one concise question.
   - If an item is not found or cannot be verified, write "未找到可靠信息" instead of guessing.

2. Find and verify source code.
   - Do not rely only on links embedded in the paper.
   - If the paper page does not explicitly provide a GitHub repository, actively search the web and GitHub for the paper title, method name, and key author names.
   - Search examples: `"<paper title>" GitHub`, `"<method name>" GitHub`, `site:github.com "<method name>"`.
   - If ordinary web search misses GitHub results or `gh search repos` is unavailable because authentication is missing, query the GitHub Search API directly before reporting no code.
   - Use normalized variants such as removing punctuation, changing hyphens/underscores, and compacting names.
   - Verify whether a candidate repository is official before reporting it. Treat GitHub as official only when at least one strong signal exists: the paper page or project page links to the repository; the repository README says it is the official implementation and links back to the exact paper; the repository owner, maintainers, commits, or contact information match the paper authors or lab; released configs, checkpoints, or results match the paper. Stars, repo name similarity, or third-party reproduction status alone are not enough.
   - For source code, provide only GitHub repository URLs. Do not provide Gitee, GitLab, Hugging Face, project pages, or zip links as the "源码地址" unless no GitHub repository exists; in that case write `- 源码地址：未找到官方 GitHub 地址` and `- 下载命令：无`.

3. Output only the requested fixed fields in Chinese.
   - Do not add extra fields or extra links outside these fields. For example, do not separately output project page, DOI, blog, dataset page, demo, license, notes, explanations, or verification summaries unless the user explicitly asks.
   - Each top-level field must start with a bullet point.
   - Put the field value after the colon on the same line whenever it fits.
   - If a value is too long and must wrap, indent continuation lines under that field.
   - For "论文地址", output only the arXiv abstract URL, such as `https://arxiv.org/abs/<id>`. Do not output CVF, OpenReview, DOI, project page, PDF, HTML, or publisher pages in this field. If no arXiv page is found, write "未找到可靠信息".
   - For "发布时间", report the first reliable public release date. Prefer arXiv v1 submission date; if no arXiv exists, use official proceedings or publisher online date; if only venue year is known, report the year. Use `YYYY-MM-DD` when available.
   - For "相关团队", prefer affiliations, labs, universities, companies, and research groups. Do not list all authors when reliable affiliation/team information is available. Summarize unique affiliations/labs/companies instead of every author-affiliation pair, keep it within one line when possible, and prefer major contributing institutions over exhaustive lists. If no affiliation is found, use the author list as a fallback.
   - For "会议 / 期刊", report only the concise verified venue/journal name and year when found, such as `ICCV 2023`, `NeurIPS 2024`, or `IEEE TPAMI 2025`. Do not include proceedings titles, publisher page links, DOI links, paper links, or explanatory URLs in this field. Only write `arXiv 预印本；未找到可靠的会议 / 期刊录用信息。` after checking exact-title venue sources beyond arXiv, including CVF/OpenReview/IEEE/ACM/Springer or the relevant official proceedings for the field.
   - For "任务类型", use concise task categories such as `检测`, `分割`, `生成`, `推荐`, `强化学习`, `多模态`, `图学习`, or a close domain-specific label.
   - For "方法类型", use concise method families such as `Transformer`, `Diffusion`, `CNN`, `Mamba`, `GNN`, `RL`, `MoE`, `蒸馏`, or a compact combination when the paper clearly spans multiple families.
   - For "一句话描述", write one short sentence summarizing what the paper does and how, without adding citations or extra links.

~~~markdown
- 论文名称：
- 论文地址：<arXiv abstract URL only, or 未找到可靠信息>
- 发布时间：<first reliable public release date, preferably arXiv v1 date in YYYY-MM-DD>
- 相关团队：<unique major affiliations/labs/groups; avoid author list when affiliations are available>
- 会议 / 期刊：<concise venue/journal name and year only, e.g. ICCV 2023; or arXiv 预印本；未找到可靠的会议 / 期刊录用信息。>
- 源码地址：<official GitHub URL, or 未找到官方 GitHub 地址>
- 下载命令：
    - 本地下载命令
        ```bash
        cd /Users/enzo/Documents/code
        git clone <official GitHub URL>
        ```
    - 远程下载命令
        ```bash
        cd /home/enzo/Documents/code
        git clone <official GitHub URL>
        ```
- 任务类型：
- 方法类型：
- 一句话描述：
~~~

4. The download command must be a `git clone` command using the official GitHub URL when a GitHub repository is found.
   - Always label the two command blocks as "本地下载命令" and "远程下载命令".
   - Put each command block inside a fenced `bash` code block under its label.
   - For the local command, use `cd /Users/enzo/Documents/code`.
   - For the remote command, use `cd /home/enzo/Documents/code`.
   - If no GitHub repository is found, write `- 下载命令：无`.

## Do Not

- Do not add verification notes, source explanations, or extra links outside the fixed fields.
- Do not put CVF, OpenReview, DOI, project page, PDF, HTML, or publisher page links under "论文地址".
- Do not report unofficial reproduction repositories as "源码地址".
- Do not infer venue from arXiv comments unless cross-checked.
