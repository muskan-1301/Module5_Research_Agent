# Reflection: Source-Cited Solar Energy Research Agent

## Benefits and limits of grounding

Grounding the agent in external sources changed the output from a general answer into a checkable one. The solar energy brief has numbered citations that map to real URLs on the References list, so a reader can open each source and check the claim. The scorecard confirmed that the brief drew on four unique domains (earth.org, energy.gov, en.wikipedia.org and environmentalscience.org), which is more than the required three.

Grounding has limits. The brief is only as good as what the search returns and what survives page cleaning. Grounding also doesn't guarantee faithfulness. In my run, one quote was cut off mid-sentence and another was a page title rather than a claim from the article. The automated checks counted both as quotes, so the scorecard passed, but a human reader would not accept them. Grounding gives you sources to check against, but you still have to check them.

## How tool use affects cost and latency

Each tool call is an extra model round trip, and each fetched page adds text to the context the model has to read. The agent called search, the Wikipedia summary, and the page fetch tool for each selected source before writing. That sequence is what makes the run slow, because each step waits for the one before it. Cleaning page text and capping it at a fixed length kept the context from growing too large. Selecting about five sources also limited the number of fetches, at the cost of less material for the brief.

## How prompting shaped the brief

The system instructions had the largest effect on quality. Describing the steps in order (search, background, select, fetch, log, write) made tool use predictable instead of leaving the model to decide whether to search at all. Specific formatting rules made the output easy to check automatically: a citation in each paragraph, quotes of ten words or fewer in double quotation marks, and no References section written by the model. Asking the model to use only fetched text reduced unsupported claims, but it didn't remove them. The brief still includes a figure about utility-scale costs that needs checking against the cited page.

Prompting is a request, not a guarantee. That's why the synthesis step handles some of the output deterministically: it rebuilds the References list from the logged sources, removes citation numbers that don't exist, and adds quotes from retrieved text when the model gives too few. The first version of the synthesis step produced only one paragraph, which failed the paragraph requirement, so I added padding from the source text.

## Challenges with HTML content

Web pages contain a lot of material that isn't content. Navigation menus, banners, footers and related-article lists would all add noise to the brief and waste context. The cleaning step removes script, style, navigation, header, footer and similar tags, prefers the main article element, and keeps only paragraphs and list items above a minimum length. It also removes repeated lines.

The approach is still heuristic. Some pages load their content with JavaScript and return little to a plain request, some block automated access, and some put real content inside elements the filter discards. The agent skips empty pages, but the brief can still lose material without any warning.

## Extensions

Three improvements would make the system more reliable. First, reranking: the search order is the search engine's, but embedding the snippets and ranking them against the topic would select more relevant sources. Second, caching: storing fetched pages and search results by URL or query would reduce cost and latency for repeated runs and make results reproducible for grading. Third, safety controls: a domain allow list, a limit on tool calls per run, and treating fetched text as untrusted data would protect against pages that try to inject instructions into the model.

A faithfulness check would also help. Automatically confirming that each quote and cited claim appears in its cited snippet would turn the current structural checks into real grounding checks, and would have caught the cut-off and title quotes in my brief.
