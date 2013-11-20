---
layout: post
title: Cookbook&#58; Extracting URL content
---

This post provides a simple recipe of how to extract website contents from given URLs.
Methods are static, since they are basic utilities and easy to reuse.

The method below receives the URL of the desired page, creates a connection, downloads its contents and replaces special characters from HTML accordingly.

Here I'm using `StringBuilder` because of possible [performance issues](/post/improving-performance-with-stringbuilder).
`StringEscapeUtils.unescapeHtml4(...)` is a ready-to-use method to decode HTML special characters.

```java
private static String getURLContent(URL url) {

	// Holds the URL content
	StringBuilder urlContent = new StringBuilder();

	try {
		// open connection to the provided URL
		URLConnection urlConn = url.openConnection();
		// copy content to a buffer
		BufferedReader urlContentBuffer = new BufferedReader(
						new InputStreamReader(urlConn.getInputStream()));

		// using StringBuilder instead of String concatenation within a loop
		// for performance reasons, since Strings are immutable.
		String urlLine = "";
		while ((urlLine = urlContentBuffer.readLine()) != null) {
				urlContent.append(urlLine);
		}
		
		// close the buffered reader
		urlContentBuffer.close();

	} catch (IOException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}

	// using Apache Commons Lang to obtain HTML special characters properly
	return StringEscapeUtils.unescapeHtml4(urlContent.toString());
}
```

I'm using a `getURLContent(String urlStr)` to encapsulate the method above with an additional feature to add the `http://` protocol in case it's not provided.

```java
public static String getURLContent(String urlStr) {

	String urlContent = null;
	try {
		// complete the url with "http://" in case the given one does not
		// provide the protocol (https, ftp etc)
		if (!urlStr.toLowerCase().contains("://")) {
				urlStr = "http://" + urlStr;
		}

		URL url = new URL(urlStr);
		urlContent = getURLContent(url);
	} catch (MalformedURLException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}
	return urlContent;
}
```

Voilà! The URL content can be easily extracted into a String using the `getURLContent(String urlStr)`.
Remember to treat the Exceptions in the way that fits best your business requirements!