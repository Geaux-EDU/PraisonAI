# Firecrawl PraisonAI Integration

<iframe width="560" height="315" src="https://www.youtube.com/embed/UoqUDcLcOYo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Firecrawl running in Localhost:3002

```python
from firecrawl import FirecrawlApp
from praisonai_tools import BaseTool
import re

class WebPageScraperTool(BaseTool):
    name: str = "Web Page Scraper Tool"
    description: str = "Scrape and extract information from a given web page URL."

    def _run(self, url: str) -> str:
        app = FirecrawlApp(api_url='http://localhost:3002')
        response = app.scrape_url(url=url)
        content = response["content"]
        # Remove all content above the line "========================================================"
        if "========================================================" in content:
            content = content.split("========================================================", 1)[1]

        # Remove all menu items and similar patterns
        content = re.sub(r'\*\s+\[.*?\]\(.*?\)', '', content)
        content = re.sub(r'\[Skip to the content\]\(.*?\)', '', content)
        content = re.sub(r'\[.*?\]\(.*?\)', '', content)
        content = re.sub(r'\s*Menu\s*', '', content)
        content = re.sub(r'\s*Search\s*', '', content)
        content = re.sub(r'Categories\s*', '', content)

        # Remove all URLs
        content = re.sub(r'http\S+', '', content)
        
        # Remove empty lines or lines with only whitespace
        content = '\n'.join([line for line in content.split('\n') if line.strip()])

        # Limit to the first 1000 words
        words = content.split()
        if len(words) > 1000:
            content = ' '.join(words[:1000])
        
        return content
```