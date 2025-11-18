# Style XML Feeds Without XSLT

So, you have an RSS feed or Atom XML feed, and you'd like it to look nice when users click on it.

Historically, the most common way to do this was XSLT. But you don't have to use XSLT for this! (You never did, really.)

## Style XML feeds with CSS

Here's an example Atom feed:

```xml
<?xml version="1.0" encoding="utf-8"?>
<feed xmlns="http://www.w3.org/2005/Atom">

    <title>Example Feed</title>
    <link href="http://example.org/"/>
    <updated>2003-12-13T18:30:02Z</updated>
    <author>
    <name>John Doe</name>
    </author>
    <id>urn:uuid:60a76c80-d399-11d9-b93C-0003939e0af6</id>

    <entry>
        <title>Atom-Powered Robots Run Amok</title>
        <link href="http://example.org/2003/12/13/atom03"/>
        <id>urn:uuid:1225c695-cfb8-4ebb-aaaa-80da344efa6a</id>
        <updated>2003-12-13T18:30:02Z</updated>
        <summary>Some text.</summary>
    </entry>

</feed>
```

You can add CSS styles to it by adding a `<link>` tag with a special `xmlns="http://www.w3.org/1999/xhtml"` namespace, like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<feed xmlns="http://www.w3.org/2005/Atom">
    <link rel="stylesheet" href="style.css" xmlns="http://www.w3.org/1999/xhtml" />
    <!-- ... the rest of the feed goes here ... -->
```

Or, you can add styles directly in the document with a `<style>` tag.

```xml
<?xml version="1.0" encoding="utf-8"?>
<feed xmlns="http://www.w3.org/2005/Atom">
    <style xmlns="http://www.w3.org/1999/xhtml"><![CDATA[
        * { color: red; }
    ]]></style>
    <!-- ... the rest of the feed goes here ... -->
```

> [!WARNING]
> You can only style XML this way when you serve the XML with `Content-Type: application/xml`. If you serve the content with `Content-Type: application/rss+xml` or `Content-Type: application/atom+xml`, it won't be processed.
> 
> (This restriction also applies to XSLT; you can't attach an XSLT stylesheet to `application/rss+xml` or `application/atom+xml` content.)

> [!NOTE]
> These styles will only display when users navigate directly to your feed URL in their browser. The styles you set here will have no effect on the appearance of your feed in a feed reader.

## Run JavaScript when users browse to your XML feeds

To run JavaScript when a user navigates to your feed, you can add a `<script>` tag to the `<feed>` element, as long as you add a special `xmlns="http://www.w3.org/1999/xhtml"` namespace to it, like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<feed xmlns="http://www.w3.org/2005/Atom">
    <script src="atom-html-transformer.js" xmlns="http://www.w3.org/1999/xhtml" />
    <!-- ... the rest of the feed goes here ... -->
```

Or, you can run script inline, like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<feed xmlns="http://www.w3.org/2005/Atom">
    <script xmlns="http://www.w3.org/1999/xhtml"><![CDATA[
        console.log('hello');
    ]]></script>
    <!-- ... the rest of the feed goes here ... -->
```

You can use JavaScript to transform your XML. Here are some ways you might want to transform your XML in JS that would be difficult or even impossible to do in CSS.

* Convert `<content type='html'>` into rendered HTML
* Add a `<head>` element, with a `<title>` and a `<meta name="viewport" content="width=device-width">` so your feed looks OK on mobile.
* Convert `<title>` elements with sibling `<link>` elements into `<a href="${link}">${title}</a>` clickable links
* Convert `<title>` elements into `<h1>`/`<h2>` elements (for screen-reader accessibility)
* Format date elements (`<published>`, `<updated>`)
* Convert person elements (`<author>`, `<contributor>`) into `<a href="${link}">${name}</a>`
* Add some extra header UI. (Perhaps you'd like to include a paragraph explaining what a feed is and how to use it?)

Some of these are possible in pure CSS with clever application of `::before`, but, life's too short to mess around with that stuff.

I've provided a drop-in example in [`atom-html-transformer.js`](atom-html-transformer.js) and [`rss-html-transformer.js`](rss-html-transformer.js).

It's a decent starting point for you to add additional transformations and bring your own CSS.

## Examples

* [Atom example](https://dfabulich.github.io/style-xml-feeds-without-xslt/atom-example.xml)
* [RSS example](https://dfabulich.github.io/style-xml-feeds-without-xslt/rss-example.xml)