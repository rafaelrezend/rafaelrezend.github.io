---
layout: post
title: Crawler4j&#58; Open-source Web Crawler for Java
---

Weeks ago I was given a task to read values from an e-commerce website.
The idea was simple: a link was given, the application should parse the content of the HTML, download the specific value and store it.
I decided to use a crawler instead, and started looking for open-source solutions for Java with fast implementation.

I finally came across [crawler4j](https://code.google.com/p/crawler4j/), which proved to be simple but very efficient right away!
So, below I show the implementation that fits my needs: *simply store all available links within a given domain, filtering the extensions which are **not** of my interest (i.e. images, videos, stylesheet).*

For the implementation, I used one class **(ProductCrawler)** to define the crawler's behaviour, and another **(CrawlerControl)** for... you guessed it.
The code is well commented (good practice!), no much need for further explanation.

```java
public class ProductCrawler extends WebCrawler {

	private final static Pattern FILTERS = Pattern.compile(
		".*(\\.(css|js|bmp|gif|jpe?g"
		+ "|png|tiff?|mid|mp2|mp3|mp4"
		+ "|wav|avi|mov|mpeg|ram|m4v|pdf"
		+ "|rm|smil|wmv|swf|wma|zip|rar|gz))$"
	);

	/*
	 * (non-Javadoc) Limit the domain of search.
	 * @see edu.uci.ics.crawler4j.crawler.WebCrawler#shouldVisit(edu.uci.ics.crawler4j.url.WebURL)
	 */
	@Override
	public boolean shouldVisit(WebURL url) {
		String href = url.getURL().toLowerCase();
		return !FILTERS.matcher(href).matches()	&& href.startsWith("http://rafaelrezend.github.io/");
	}

	/*
	 * (non-Javadoc) Action to take when a page is visited.
	 * @see edu.uci.ics.crawler4j.crawler.WebCrawler#visit(edu.uci.ics.crawler4j.crawler.Page)
	 */
	@Override
	public void visit(Page page) {
		// get its own URL
		String url = page.getWebURL().getURL();
		System.out.println("Adding URL to list: " + url);

		// get the singleton instance of the ProductList
		LinksList pl = LinksList.getInstance();

		// store the url
		pl.put(url);
	}
}
```

This web crawler is a producer of product links (It's was developed for an e-commerce). It writes links to a global singleton `pl`.

Further improvement could be to check if the current webpage has the target content before adding to the list.
However, this check could be cpu consuming, so the current implementation adds every link - excepts those filtered by extension - to the product list to be evaluated afterwards (or in parallel) outside the crawler implementation.

The crawler controller is simple:

```java
public class CrawlerControl {

	public static void startCrawler() {

		CrawlConfig config = new CrawlConfig();

		// folder where intermediate crawl data is stored.
		config.setCrawlStorageFolder("crawlerData");

		/*
		 * Be polite: Make sure that we don't send more than 1 request per
		 * second (1000 milliseconds between requests).
		 */
		config.setPolitenessDelay(1000);

		/*
		 * This config parameter can be used to set your crawl to be resumable
		 * (meaning that you can resume the crawl from a previously
		 * interrupted/crashed crawl). Note: if you enable resuming feature and
		 * want to start a fresh crawl, you need to delete the contents of
		 * rootFolder manually.
		 */
		config.setResumableCrawling(false);

		/*
		 * Instantiate the controller for this crawl.
		 */
		PageFetcher pageFetcher = new PageFetcher(config);
		RobotstxtConfig robotstxtConfig = new RobotstxtConfig();
		RobotstxtServer robotstxtServer = new RobotstxtServer(robotstxtConfig,
						pageFetcher);

		try {
			CrawlController controller = new CrawlController(config,
							pageFetcher, robotstxtServer);

			// starting page
			controller.addSeed("http://rafaelrezend.github.io/");

			/*
			 * Start the crawl. This is a blocking operation, meaning that your
			 * code will reach the line after this only when crawling is
			 * finished. The parameter '1' corresponds to the number of threads.
			 * A single thread is sufficient for this case.
			 */
			controller.startNonBlocking(ProductCrawler.class, 1);
		} catch (Exception e) {
				e.printStackTrace();
		}
	}
}
```

Then, in the main file, it is invoked as simply as this:

```java
// creation of the unique instance containing the list of links
LinksList pList = LinksList.getInstance();

// starts the web crawler in parallel (this crawler is non blocking)
CrawlerControl.startCrawler();
```

Obviously, this is the shortest way to get your crawler running, as you could simply ignore the [whole theory](http://en.wikipedia.org/wiki/Web_crawler) behind it.
Some properties can dramatically change your results, especially in the long run, if you need to keep your crawler running indefinitely and updating the content that has already been tracked. For instance, there are different algorithms for revisit policy, parallelism, politeness etc, but there is not a general solution that fits all. Every requirement should be study carefully for optimal efficiency.

The code above is available on my GitHub [repository](https://github.com/rafaelrezend/eCommerceCrawler/tree/master/src/rezend/ecomm/crawler).
