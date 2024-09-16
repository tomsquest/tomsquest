# 👋 Hey, I'm Thomas Queste

## Who am I?

I'm a **developer**, and I build **products**.

Since pre-2000, I've been turning lines of code into rewarding creations,
  awkward bugs, and unexpected dance parties when the code finally works! 🎉

## Highlights

- Running my own company since 2013
- Founded three startups
- Quick transition from idea to execution — I call it Extreme Pragmatism (Better to have a database in a JSON file than fancy slides)
- Certified in Java, ElasticSearch, and MongoDB, but also Finance
- Beginner in Machine Learning (classification, Learning-to-Rank, Deep learning...)

## Fun Facts

- In 1999, my first website was fully designed with HTML tables (didn't know about divs!)
- I enjoy running and riding bicycles in all weather, from -15°C to 40°C
- As a minimalist, I've been running in zero drop shoes since 2008 (proud owner of Xero shoes)

## Links

- <a href="https://www.tomsquest.com" title="blog">tomsquest.com</a> 💕
- <a href="https://www.linkedin.com/in/thomasqueste" title="View Thomas Queste's profile on LinkedIn">LinkedIn</a>
- <a href="https://twitter.com/ThomasQueste" title="View Thomas Queste's profile on Twitter">X/Twitter</a>

## Latest Blog Posts ✍️

<!-- BLOG-POST-LIST:START -->
🔥 2024-08-31 
 [Get structured output from a Language Model using BAML](https://www.tomsquest.com/blog/2024/08/get-structured-output-from-llm-using-baml/) 
 > &lt;p&gt;What if your LLM produces invalid JSON, and worse, JSON that does not respect your schema? 
Let us dig into &lt;a href=&quot;https://github.com/BoundaryML/baml&quot;&gt;BAML&lt;/a&gt;, a tool to solve that, and more!&lt;/p&gt;

&lt;p&gt;Update&lpar;2024-09-02&rpar;: Add the &lt;a href=&quot;https://github.com/tomsquest/llm_extract_books&quot;&gt;repository of the project&lt;/a&gt;&lt;/p&gt;

&lt;h2 id=&quot;tldr&quot;&gt;TL;DR&lt;/h2&gt;

&lt;ul&gt;
  &lt;li&gt;LLMs are not consistent at producing JSON, but getting better at this&lt;/li&gt;
  &lt;li&gt;Multiple solutions: function calling, GPT4o strict mode, JSON repair &amp;amp; JSON schema, or everything at once: BAML&lt;/li&gt;
  &lt;li&gt;&lt;a href=&quot;https://github.com/BoundaryML/baml&quot;&gt;BAML&lt;/a&gt; solves the problem and more: retries, cascading between LLM, compatible with multiple LLM, multiple languages, a prompt workbench…&lt;/li&gt;
  &lt;li&gt;Show me the code: &lt;a href=&quot;https://github.com/tomsquest/llm_extract_books&quot;&gt;tomsquest/llm_extract_books&lt;/a&gt;&lt;/li&gt;
&lt;/ul&gt;

&lt;h2 id=&quot;the-problem&quot;&gt;The problem&lt;/h2&gt;

&lt;p&gt;In August, my &lt;strong&gt;Quest&lt;/strong&gt; was to convert my list of books from a text file to &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;JSON&lt;/code&gt; to give it a better structure, and to eventually publish it &lpar;see &lt;a href=&quot;/bookshelf/&quot;&gt;Bookshelf&lt;/a&gt;&rpar;.&lt;/p&gt;

&lt;p&gt;A typical note about a book looked like this:&lt;/p&gt;

&lt;div class=&quot;language-plaintext highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;Awesome book - Author
Read on august 24
Rating: 4
A good read, but dumb ending
https://book.com/awesome
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;Or worse:&lt;/p&gt;

&lt;div class=&quot;language-plaintext highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;Garrett - 6 - Glen Cook
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;&lpar;which means I read the volume 6 of the series “Garrett” by Glen Cook&rpar;&lt;/p&gt;

&lt;p&gt;I expected a JSON like this:&lt;/p&gt;

&lt;div class=&quot;language-json highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;title&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;&quot;Awesome book&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;author&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;&quot;Authon&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;read_date&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;&quot;2024-08-24&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;rating&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;mi&quot;&gt;4&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;comment&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;&quot;A good read, bad ending&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;url&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;&quot;https://book.com/awesome&quot;&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;}&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
&lt;/span&gt;&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;Easy, right?&lt;/p&gt;

&lt;p&gt;But:&lt;/p&gt;
&lt;ol&gt;
  &lt;li&gt;My notes are far from being consistent: multiple date format, no or inconsistent series, many authors, no rating, multiline comments, a description…&lt;/li&gt;
  &lt;li&gt;LLM failed sometimes to produce a valid JSON or do not respect the given schema &lpar;I am looking at you Gemini 😡&rpar;&lt;/li&gt;
&lt;/ol&gt;

&lt;p&gt;As invalid JSON, I got:&lt;/p&gt;
&lt;ul&gt;
  &lt;li&gt;Some wrong syntax: &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;&quot;field&quot;: wrong&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;Collapsed fields: &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;&quot;field&quot;: &quot;wrong&quot; &quot;field2&quot;: &quot;wrong2&quot;&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;New unknown fields &lpar;even with a temperature of 0&rpar;&lt;/li&gt;
  &lt;li&gt;Missing fields&lt;/li&gt;
  &lt;li&gt;Unknown fields&lt;/li&gt;
  &lt;li&gt;Wrong types: &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;&quot;rating&quot;: &quot;4&quot;&lt;/code&gt; &lpar;string instead of int&rpar;, &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;volume: 1&lt;/code&gt; &lpar;int instead of string&rpar;…&lt;/li&gt;
&lt;/ul&gt;

&lt;h2 id=&quot;a-solution-give-a-schema-and-hope-for-the-best&quot;&gt;A solution: give a schema, and hope for the best&lt;/h2&gt;

&lt;p&gt;If you just want to repair the “string” &lpar;the JSON string&rpar;, you can use &lt;a href=&quot;https://github.com/mangiucugna/json_repair/&quot;&gt;json_repair&lt;/a&gt; in Python.&lt;/p&gt;

&lt;p&gt;Something like:&lt;/p&gt;

&lt;div class=&quot;language-python highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;kn&quot;&gt;from&lt;/span&gt; &lt;span class=&quot;nn&quot;&gt;json_repair&lt;/span&gt; &lt;span class=&quot;kn&quot;&gt;import&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;repair_json&lt;/span&gt;

&lt;span class=&quot;n&quot;&gt;good_json_string&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;repair_json&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;n&quot;&gt;bad_json_string&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt;
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;But if you want to respect a structure, you need to give the LLM a schema, usually in the form of a JSON schema.&lt;/p&gt;

&lt;p&gt;Have a look at this article from Pydantic: “&lt;a href=&quot;https://pydantic.dev/articles/llm-intro&quot;&gt;Steering Large Language Models with Pydantic&lt;/a&gt;”.&lt;/p&gt;

&lt;p&gt;And/Or you need something like &lt;a href=&quot;https://docs.pydantic.dev/latest/concepts/json/#partial-json-parsing&quot;&gt;Pydantic Partial JSON Parsing&lt;/a&gt; to validate it.&lt;/p&gt;

&lt;div class=&quot;language-python highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;kn&quot;&gt;from&lt;/span&gt; &lt;span class=&quot;nn&quot;&gt;pydantic_core&lt;/span&gt; &lt;span class=&quot;kn&quot;&gt;import&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;from_json&lt;/span&gt;

&lt;span class=&quot;n&quot;&gt;partial_json_data&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;&#39;[&quot;aa&quot;, &quot;bb&quot;, &quot;c&#39;&lt;/span&gt;  

&lt;span class=&quot;n&quot;&gt;result&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;from_json&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;n&quot;&gt;partial_json_data&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;allow_partial&lt;/span&gt;&lt;span class=&quot;o&quot;&gt;=&lt;/span&gt;&lt;span class=&quot;bp&quot;&gt;True&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt;
&lt;span class=&quot;c1&quot;&gt;#&amp;gt; [&#39;aa&#39;, &#39;bb&#39;]
&lt;/span&gt;&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;Still, you need to connect to the LLM, and maybe you will need some retry strategy, or better you use a light and cheap model, and if it fails, you then use a more powerful one.&lt;/p&gt;

&lt;p&gt;I discovered &lt;a href=&quot;https://github.com/BoundaryML/baml&quot;&gt;BAML&lt;/a&gt;, a tool to solve this problem.&lt;/p&gt;

&lt;h2 id=&quot;the-solution-baml&quot;&gt;The solution: BAML&lt;/h2&gt;

&lt;p&gt;The &lt;a href=&quot;https://docs.boundaryml.com/&quot;&gt;documentation of BAML&lt;/a&gt; is quite clear.&lt;br /&gt;
I just want to give an overview of what I did using the tool.&lt;/p&gt;

&lt;h3 id=&quot;prompt-workbench&quot;&gt;Prompt workbench&lt;/h3&gt;

&lt;p&gt;The quest starts with the &lt;a href=&quot;https://www.promptfiddle.com/extract-book-info-xFZSd&quot;&gt;workbench at PromptFiddle&lt;/a&gt;.&lt;/p&gt;

&lt;p&gt;&lt;img src=&quot;/assets/images/posts/2024-08-31-get-structured-output-from-llm-using-baml/promptfilddle.jpg&quot; alt=&quot;promptfilddle.jpg&quot; /&gt;&lt;/p&gt;

&lt;p&gt;The workbench is an editor where you write your prompt and tests, and see the results, using the LLM of your choice.&lt;/p&gt;

&lt;p&gt;By the way, BoundaryML provides a free way to use models like Gpt4, Claude…!&lt;/p&gt;

&lt;p&gt;You can have a look at my prompt and run the test cases:&lt;br /&gt;
&lt;a href=&quot;https://www.promptfiddle.com/extract-book-info-xFZSd&quot;&gt;https://www.promptfiddle.com/extract-book-info-xFZSd&lt;/a&gt;&lt;/p&gt;

&lt;p&gt;A baml file consists of:&lt;/p&gt;
&lt;ul&gt;
  &lt;li&gt;a structure that will define what the LLM should output&lt;/li&gt;
  &lt;li&gt;a prompt in a “function” that will give the LLM the context and the data to process&lt;/li&gt;
  &lt;li&gt;some test cases to validate the output in the workbench&lt;/li&gt;
&lt;/ul&gt;

&lt;p&gt;And another file, &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;clients.baml&lt;/code&gt;, that defines which available LLMs you can use.&lt;/p&gt;

&lt;p&gt;Example &lpar;shorten for clarity&rpar;:&lt;/p&gt;

&lt;div class=&quot;language-plaintext highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;class Book {
  title string
  authors string[]
  rating int?
  date string? @description&lpar;&quot;format as yyyy-mm if possible&quot;&rpar;
}

function ExtractBook&lpar;book_text: string&rpar; -&amp;gt; Book {
  client ClaudeHaiku // define which client to use here

  prompt #&quot;
    Your task is to extract structured information from the book and format it as JSON.

    Book:
    ---
    {{ book_text }}
    ---

    {# special macro to print the output instructions. #}
    {{ ctx.output_format }}

    JSON:
  &quot;#
}

test Test_complete {
  functions [ExtractBook]
  args {
    book_text #&quot;
      Les feux de Cibola - The Expanse - Tome 4/7 - James S.A. Corey
      Lu en avril 2024
      4 sur 5
      Je prends assez de plaisir à lire la suite de l&#39;évolution du monde de The Expanse.
      Le héros est un peu comme Harry Potter, mais ca passe.
      [Roubaix]&lpar;http://www.mediathequederoubaix.fr/ark:/245243523452&rpar;
      [Amazon]&lpar;https://www.amazon.fr/Quality-Land-Marc-Uwe-Kling/dp/13414321&rpar;
    &quot;#
  }
}

test Test_mini {
  functions [ExtractBook]
  args {
    book_text #&quot;
      Garrett - Glen Cook
    &quot;#
  }
}
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;&lt;strong&gt;Remark&lt;/strong&gt;: No JSON schema here, because from what I get, it consumes lots of tokens, while not being more efficient, thus not producing better results.&lt;/p&gt;

&lt;h3 id=&quot;generating-a-client&quot;&gt;Generating a client&lt;/h3&gt;

&lt;p&gt;In your project, you store the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;baml&lt;/code&gt; files, and you can generate a client using the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;baml&lt;/code&gt; command line tool.&lt;/p&gt;

&lt;p&gt;BAML can generate clients in Python, Typescript, and Ruby currently.&lt;/p&gt;

&lt;p&gt;Typically:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;c&quot;&gt;# files in baml_src &lt;/span&gt;
book.baml
clients.baml
generators.baml
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;Then generate the actual code, here in Python:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;nv&quot;&gt;$ &lt;/span&gt;baml-cli generate
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;You get a &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;baml_client&lt;/code&gt; directory.&lt;/p&gt;

&lt;h3 id=&quot;usage&quot;&gt;Usage&lt;/h3&gt;

&lt;p&gt;From the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;baml_client&lt;/code&gt; directory, you can use the generated code to extract the information from the LLM.&lt;/p&gt;

&lt;div class=&quot;language-python highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;kn&quot;&gt;from&lt;/span&gt; &lt;span class=&quot;nn&quot;&gt;baml_client.types&lt;/span&gt; &lt;span class=&quot;kn&quot;&gt;import&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;Book&lt;/span&gt;

&lt;span class=&quot;n&quot;&gt;book&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;extract_book&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;n&quot;&gt;book_text&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt;
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;Tips: if you want to specify the model to use at runtime, you can use the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;ClientRegistry&lt;/code&gt;:&lt;/p&gt;

&lt;div class=&quot;language-python highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;kn&quot;&gt;from&lt;/span&gt; &lt;span class=&quot;nn&quot;&gt;baml_py.baml_py&lt;/span&gt; &lt;span class=&quot;kn&quot;&gt;import&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;ClientRegistry&lt;/span&gt;
&lt;span class=&quot;kn&quot;&gt;from&lt;/span&gt; &lt;span class=&quot;nn&quot;&gt;baml_client&lt;/span&gt; &lt;span class=&quot;kn&quot;&gt;import&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;b&lt;/span&gt;
&lt;span class=&quot;kn&quot;&gt;from&lt;/span&gt; &lt;span class=&quot;nn&quot;&gt;baml_client.types&lt;/span&gt; &lt;span class=&quot;kn&quot;&gt;import&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;Book&lt;/span&gt;


&lt;span class=&quot;k&quot;&gt;class&lt;/span&gt; &lt;span class=&quot;nc&quot;&gt;Model&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nb&quot;&gt;str&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;Enum&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;:&lt;/span&gt;
    &lt;span class=&quot;n&quot;&gt;ClaudeHaiku&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;&quot;ClaudeHaiku&quot;&lt;/span&gt;
    &lt;span class=&quot;n&quot;&gt;ClaudeSonnet&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;&quot;ClaudeSonnet&quot;&lt;/span&gt;
    &lt;span class=&quot;n&quot;&gt;GeminiFlash&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;&quot;GeminiFlash&quot;&lt;/span&gt;
    &lt;span class=&quot;n&quot;&gt;GeminiPro&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;&quot;GeminiPro&quot;&lt;/span&gt;


&lt;span class=&quot;k&quot;&gt;def&lt;/span&gt; &lt;span class=&quot;nf&quot;&gt;extract_book&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;n&quot;&gt;book_text&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;nb&quot;&gt;str&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;model&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;Model&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;-&amp;gt;&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;Book&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;
    &lt;span class=&quot;n&quot;&gt;client_registry&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;ClientRegistry&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&rpar;&lt;/span&gt;
    &lt;span class=&quot;n&quot;&gt;client_registry&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;n&quot;&gt;set_primary&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;n&quot;&gt;model&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt;
    &lt;span class=&quot;n&quot;&gt;book&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;b&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;n&quot;&gt;ExtractBook&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;n&quot;&gt;book_text&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;&lt;span class=&quot;s&quot;&gt;&quot;client_registry&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;client_registry&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;}&rpar;&lt;/span&gt;
    &lt;span class=&quot;k&quot;&gt;return&lt;/span&gt; &lt;span class=&quot;n&quot;&gt;book&lt;/span&gt;
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h3 id=&quot;llm-config-retry-failover&quot;&gt;LLM config, Retry, failover&lt;/h3&gt;

&lt;p&gt;I set the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;temperature&lt;/code&gt; to &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;0&lt;/code&gt; in the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;clients.baml&lt;/code&gt; file, to get a deterministic output.&lt;/p&gt;

&lt;p&gt;Bonus: you can specify a retry strategy, and a failover strategy, in the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;clients.baml&lt;/code&gt; file.&lt;/p&gt;

&lt;p&gt;Here is what I used &lpar;I added a retry due to a network error when calling Anthropic, this happened once&rpar;:&lt;/p&gt;

&lt;div class=&quot;language-plaintext highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;retry_policy RetryTwice {
  max_retries 2
}

client&amp;lt;llm&amp;gt; ClaudeHaiku {
  provider anthropic
  retry_policy RetryTwice
  options {
    model claude-3-haiku-20240307
    api_key env.ANTHROPIC_API_KEY
    temperature 0
    max_tokens 1000
  }
}
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h3 id=&quot;conclusion&quot;&gt;Conclusion&lt;/h3&gt;

&lt;p&gt;A really good experience with BAML!&lt;/p&gt;

&lt;p&gt;&lt;strong&gt;Quest&lt;/strong&gt; for a structured output from a LLM: &lt;strong&gt;achieved&lt;/strong&gt; ✅  &lt;br /&gt;
&lt;strong&gt;Quest&lt;/strong&gt; for converting my books to JSON: &lt;strong&gt;achieved&lt;/strong&gt; ✅&lt;/p&gt;

&lt;p&gt;You can have a look at my &lt;a href=&quot;/bookshelf/&quot;&gt;Bookshelf&lt;/a&gt; page for the result.&lt;/p&gt;

&lt;p&gt;Head to the &lt;a href=&quot;https://github.com/tomsquest/llm_extract_books&quot;&gt;tomsquest/llm_extract_books&lt;/a&gt; repository to see the code.&lt;/p&gt; 

🚀 2024-07-27 
 [Delightful experience with Anthropic Claude LLM](https://www.tomsquest.com/blog/2024/07/using-claude-llm/) 
 > &lt;p&gt;Lately, I wanted to convert my list of books from a text file to &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;JSON&lt;/code&gt; to give it a better structure. 
Instead of writing a bunch of regexes and string tricks, I went for &lt;a href=&quot;https://www.anthropic.com/api&quot;&gt;Anthropic Claude LLM&lt;/a&gt;. 
The experience and results were impressive!&lt;/p&gt;

&lt;h2 id=&quot;tldr&quot;&gt;TL;DR&lt;/h2&gt;

&lt;ul&gt;
  &lt;li&gt;Claude was learnt to recognize tags, eg. &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;&amp;lt;book&amp;gt;&amp;lt;/books&amp;gt;&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;The workbench handles variables, eg. &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;{{ BOOKS }}&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;You can evaluate your prompt with test cases&lt;/li&gt;
  &lt;li&gt;Flawless, I got exactly the output specified, no hallucination, no wrong JSON, using &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;claude-3-5-sonnet-20240620&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;Exhausted my credit in a few runs, learning that I should not have tried to process all my book list at once&lt;/li&gt;
&lt;/ul&gt;

&lt;h2 id=&quot;problem-i-was-not-consistent&quot;&gt;Problem: I was not consistent&lt;/h2&gt;

&lt;p&gt;Today when I read a book, I simply add an entry in my &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;books.txt&lt;/code&gt; with the title, author, read date, rating, sometimes a comment…&lt;/p&gt;

&lt;p&gt;And I am not really &lt;strong&gt;consistent!&lt;/strong&gt;&lt;/p&gt;

&lt;p&gt;Sometimes I change the date format, or the rating, or forget to write the author’s name, or put the volume number in the title, or &lpar;long list of inconsistencies…&rpar;.&lt;/p&gt;

&lt;h2 id=&quot;solution-a-llm&quot;&gt;Solution: a LLM&lt;/h2&gt;

&lt;p&gt;My first approach was simple code, parsing regexes and… I quickly failed.&lt;/p&gt;

&lt;p&gt;So, why not use a smart LLM to extract structured data from my hazardous-non-strict grammar?&lt;/p&gt;

&lt;p&gt;I went to &lt;a href=&quot;https://console.anthropic.com/workbench&quot;&gt;Anthropic Claude&lt;/a&gt;, because it got impressive results in some tests I read. 
The API is not free, but you can start with a free &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;5$descriptionlt;/code&gt; credit, enough for a start.&lt;/p&gt;

&lt;h2 id=&quot;how-it-went-the-workbench&quot;&gt;How it went: The Workbench&lt;/h2&gt;

&lt;p&gt;The &lt;a href=&quot;https://console.anthropic.com&quot;&gt;workbench&lt;/a&gt; is a web interface where you can test your prompt and see the output.&lt;/p&gt;

&lt;h3 id=&quot;a-pitch-then-a-prompt&quot;&gt;A pitch then a prompt&lt;/h3&gt;

&lt;p&gt;I started with a simple sentence &lpar;basically the idea in my head&rpar; and asked Claude to generate a prompt from that “pitch.”&lt;/p&gt;

&lt;p&gt;Prompt: &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;Extract structured data from this list of books in french&lt;/code&gt;&lt;/p&gt;

&lt;p&gt;&lt;img src=&quot;/assets/images/posts/2024-07-27-using-claude-llm/workbench_prompt_generation.png&quot; alt=&quot;workbench_prompt_generation.png&quot; /&gt;&lt;/p&gt;

&lt;p&gt;Claude generated a bigger and hopefully more precise prompt, with an example structure and some instructions for itself.&lt;/p&gt;

&lt;p&gt;The temperature was already set to &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;0&lt;/code&gt;, which makes the model more deterministic and less creative.&lt;/p&gt;

&lt;p&gt;After modifying the fields for my own &lpar;like rating, date read…&rpar;, the prompt become this one:&lt;/p&gt;

&lt;div class=&quot;language-text highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;You will be given a list of books in French. Your task is to extract structured information from this list and format it as JSON. Here is the list of books:

&amp;lt;book_list&amp;gt;
{{BOOK_LIST}}
&amp;lt;/book_list&amp;gt;

For each book in the list, extract the following information:
1. Title &lpar;titre&rpar;
2. Author &lpar;auteur&rpar;
3. Description &lpar;description&rpar;
4. Rating &lpar;note&rpar;
5. Comment &lpar;commentaire&rpar;
6. Date Read &lpar;date de lecture&rpar;
7. Urls &lpar;liens&rpar;

Keep the JSON keys in English, but extract the values from the French text. If any information is missing for a particular book, use null for that field.

Format the extracted information as a JSON array of objects, where each object represents a book. Use the following structure:

```json
[
  {
    &quot;title&quot;: &quot;Book Title&quot;,
    &quot;author&quot;: &quot;Author Name&quot;,
    &quot;description&quot;: &quot;Book description&quot;,
    &quot;rating&quot;: 5,
    &quot;comment&quot;: &quot;Reader&#39;s comment&quot;,
    &quot;dateRead&quot;: &quot;2023-05-15&quot;,
    &quot;urls&quot;: [&quot;https://somewebsite.com/book&quot;]
  },
  // More books...
]
`` `

Note that the &quot;rating&quot; should be a number if available, and the &quot;dateRead&quot; should be in ISO 8601 format &lpar;YYYY-MM-DD&rpar; if possible.

Here&#39;s an example of how to structure a single book entry:

```json
{
  &quot;title&quot;: &quot;Le Petit Prince&quot;,
  &quot;author&quot;: &quot;Antoine de Saint-Exupéry&quot;,
  &quot;description&quot;: &quot;Un conte philosophique sur l&#39;amitié et la vie&quot;,
  &quot;rating&quot;: 5,
  &quot;comment&quot;: &quot;Un classique intemporel, toujours aussi touchant&quot;,
  &quot;dateRead&quot;: &quot;2023-04-10&quot;,
  &quot;urls&quot;: [&quot;https://amazon.com/12345&quot;]
}
`` `

Analyze the provided book list, extract the required information, and format it as a JSON array. If you encounter any ambiguities or difficulties in extracting certain information, use your best judgment to interpret the data.

Provide your final output enclosed in &amp;lt;json_output&amp;gt; tags.
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h3 id=&quot;variables&quot;&gt;Variables&lt;/h3&gt;

&lt;p&gt;If you have sharp eyes, you may have noticed the special &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;{{ BOOKS_LIST }}&lt;/code&gt; in red.&lt;/p&gt;

&lt;p&gt;This is a variable that you can fill in a separate panel in the workbench:&lt;/p&gt;

&lt;p&gt;&lt;img src=&quot;/assets/images/posts/2024-07-27-using-claude-llm/workbench_variables.png&quot; alt=&quot;workbench_variables.png&quot; /&gt;&lt;/p&gt;

&lt;h3 id=&quot;evaluation&quot;&gt;Evaluation&lt;/h3&gt;

&lt;p&gt;You can evaluate your prompt directly in the workbench, providing &lt;strong&gt;test cases&lt;/strong&gt;:&lt;/p&gt;

&lt;p&gt;&lt;img src=&quot;/assets/images/posts/2024-07-27-using-claude-llm/workbench_evaluate.png&quot; alt=&quot;workbench_evaluate.png&quot; /&gt;&lt;/p&gt;

&lt;h3 id=&quot;the-code&quot;&gt;The code&lt;/h3&gt;

&lt;p&gt;As for Google VertexAI, Claude gives you directly the code to execute in the API:&lt;/p&gt;

&lt;p&gt;&lt;img src=&quot;/assets/images/posts/2024-07-27-using-claude-llm/workbench_get_code.png&quot; alt=&quot;workbench_get_code.png&quot; /&gt;&lt;/p&gt;

&lt;h2 id=&quot;what-failed-lessons-learned&quot;&gt;What failed, lessons learned&lt;/h2&gt;

&lt;p&gt;&lt;strong&gt;Input can be huge, but Output is limited and more expensive&lt;/strong&gt;.&lt;/p&gt;

&lt;p&gt;I have a big list of books &lpar;a few hundreds&rpar;, and I tried to process them all at once.&lt;br /&gt;
For that, I copied my full list in the variable, and… only a few books were returned.&lt;/p&gt;

&lt;p&gt;&lt;strong&gt;Yes, indeed&lt;/strong&gt;, the output is limited to a few thousand tokens depending on the model, so you &lt;strong&gt;cannot&lt;/strong&gt; process a big list at once.&lt;/p&gt;

&lt;p&gt;Solutions:&lt;/p&gt;
&lt;ul&gt;
  &lt;li&gt;Chunk your list in &lt;strong&gt;multiple smaller lists&lt;/strong&gt;&lt;/li&gt;
  &lt;li&gt;Or, change the prompt and process books &lt;strong&gt;one by one&lt;/strong&gt;, calling the LLM for each, which is actually more simple and can even reduce the prompt size, so the tokens, so the speed and cost&lt;/li&gt;
&lt;/ul&gt;

&lt;h2 id=&quot;conclusion&quot;&gt;Conclusion&lt;/h2&gt;

&lt;p&gt;I had a good experience with Anthropic Claude LLM. 👍&lt;br /&gt;
The newest models are impressive, pretty consistent, and easy to use.&lt;/p&gt;

&lt;p&gt;But there are real differences between LLM and their capacity.&lt;br /&gt;
For instance, I also used &lt;a href=&quot;https://gemini.google.com&quot;&gt;Gemini Flash&lt;/a&gt; on the same task, and the results were not as good as Claude. 
It failed to output &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;JSON&lt;/code&gt; and only that &lpar;no Markdown wrapper…&rpar;. Same when I tried to get &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;JSONL&lt;/code&gt; &lpar;one JSON per line&rpar;.
Could be my prompt, could be the model…&lt;/p&gt; 

🔥 2024-06-22 
 [Maximizing Efficiency: A Guide to Caching in Jest, Prettier, ESLint, and TypeScript](https://www.tomsquest.com/blog/2024/06/cache-jest-eslint-prettier-typescript-ci/) 
 > &lt;p&gt;&lt;strong&gt;Cache all the things!&lt;/strong&gt; 
Speed up your development workflow by enabling caching for Jest, Prettier, ESLint, and TypeScript, both locally and in your CI.&lt;/p&gt;

&lt;h2 id=&quot;tldr&quot;&gt;TL;DR&lt;/h2&gt;

&lt;ul&gt;
  &lt;li&gt;Set up a &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;.cache&lt;/code&gt; folder in your project&lt;/li&gt;
  &lt;li&gt;Keep Jest’s cache across restart, &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;--cacheDirectory .cache/jest/&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;Enable ESLint’s cache &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;--cache --cache-location .cache/eslint/&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;Enable Prettier’s cache &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;--cache --cache-location .cache/prettier/&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;Enable TypeScript &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;incremental&lt;/code&gt; builds&lt;/li&gt;
  &lt;li&gt;CI: store the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;.cache&lt;/code&gt; folder across builds&lt;/li&gt;
&lt;/ul&gt;

&lt;h2 id=&quot;cache-folder&quot;&gt;Cache folder&lt;/h2&gt;

&lt;p&gt;Store all cache files in a &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;.cache&lt;/code&gt; folder at your project’s root
This folder will be useful to fasten your workflow locally, keep your project clean, and in the CI by storing .cache across builds.&lt;/p&gt;

&lt;p&gt;First step, add &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;.cache&lt;/code&gt; to your &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;.gitignore&lt;/code&gt;:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;nb&quot;&gt;echo&lt;/span&gt; &lt;span class=&quot;s2&quot;&gt;&quot;.cache&quot;&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;gt;&amp;gt;&lt;/span&gt; .gitignore
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h2 id=&quot;jests-cache&quot;&gt;Jest’s cache&lt;/h2&gt;

&lt;p&gt;Jest’s cache is &lt;strong&gt;enabled&lt;/strong&gt; by default, but it’s kept in &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;/tmp&lt;/code&gt; by default, so not kept across restarts and lost in the CI.&lt;/p&gt;

&lt;p&gt;Keep the cache by specifying a cache directory:&lt;/p&gt;

&lt;div class=&quot;language-json highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;jest&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
    &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;cacheDirectory&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;&quot;.cache/jest&quot;&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;p&quot;&gt;}&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;}&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
&lt;/span&gt;&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;Alternative on the command line:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;npx jest &lt;span class=&quot;nt&quot;&gt;--cacheDirectory&lt;/span&gt; .cache/jest
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h2 id=&quot;eslints-cache&quot;&gt;Eslint’s cache&lt;/h2&gt;

&lt;p&gt;From the &lt;a href=&quot;https://eslint.org/docs/latest/use/command-line-interface#caching&quot;&gt;ESLint docs&lt;/a&gt;:&lt;/p&gt;
&lt;blockquote&gt;
  &lt;p&gt;&lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;--cache&lt;/code&gt;&lt;br /&gt;
Store the info about processed files in order to only operate on the changed ones. Enabling this option can dramatically improve ESLint’s run time performance by ensuring that only changed files are linted.&lt;/p&gt;
&lt;/blockquote&gt;

&lt;p&gt;Run ESLint with the cache enabled, and specify the cache location:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;npx eslint &lt;span class=&quot;nt&quot;&gt;--cache&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;--cache-location&lt;/span&gt; .cache/eslint/
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;In your CI, you will want to change the cache strategy for &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;content&lt;/code&gt;. 
The default is &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;metadata&lt;/code&gt;, which is faster, but the file modification date change in the CI &lpar;due to Git clone&rpar;, and the cache will not be used.&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;npm run lint &lt;span class=&quot;nt&quot;&gt;--&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;--cache&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;--cache-location&lt;/span&gt; .cache/eslint/ &lt;span class=&quot;nt&quot;&gt;--cache-strategy&lt;/span&gt; content
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h2 id=&quot;prettiers-cache&quot;&gt;Prettier’s cache&lt;/h2&gt;

&lt;p&gt;Prettier’s cache is &lt;strong&gt;disabled&lt;/strong&gt; by default, but you can enable it with &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;--cache&lt;/code&gt;.
You can also set the location of the cache, and the cache strategy.&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;npx prettier &lt;span class=&quot;nt&quot;&gt;--cache&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;--cache-location&lt;/span&gt; .cache/prettier/
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;As for ESLint, you can change the cache strategy for &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;content&lt;/code&gt; in your CI:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;npm run format &lt;span class=&quot;nt&quot;&gt;--&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;--cache&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;--cache-location&lt;/span&gt; .cache/prettier/ &lt;span class=&quot;nt&quot;&gt;--cache-strategy&lt;/span&gt; content
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h2 id=&quot;typescripts-cache&quot;&gt;Typescript’s cache&lt;/h2&gt;

&lt;p&gt;Typescript uses &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;.tsbuildinfo&lt;/code&gt; files placed that cannot be placed in &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;.cache&lt;/code&gt; from what I know.&lt;/p&gt;

&lt;p&gt;From the &lt;a href=&quot;https://www.typescriptlang.org/tsconfig/#incremental&quot;&gt;TypeScript docs&lt;/a&gt;:&lt;/p&gt;
&lt;blockquote&gt;
  &lt;p&gt;&lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;incremental&lt;/code&gt;&lt;br /&gt;
Tells TypeScript to save information about the project graph from the last compilation to files stored on disk.&lt;/p&gt;
&lt;/blockquote&gt;

&lt;p&gt;Add to your &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;tsconfig.json&lt;/code&gt;:&lt;/p&gt;

&lt;div class=&quot;language-json highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;compilerOptions&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
    &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;incremental&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;kc&quot;&gt;true&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;p&quot;&gt;}&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;}&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
&lt;/span&gt;&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h2 id=&quot;ci&quot;&gt;CI&lt;/h2&gt;

&lt;p&gt;In your CI, you will want to store the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;.cache&lt;/code&gt; folder across builds.&lt;/p&gt;

&lt;p&gt;This can be achieved easily, using &lt;a href=&quot;https://github.com/magnetikonline/action-node-modules-cache&quot;&gt;Magnetikonline’s ActionNodeModules cache&lt;/a&gt;, that will cache &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;node_modules&lt;/code&gt; and any additional path you specify.&lt;/p&gt;

&lt;div class=&quot;language-yaml highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;pi&quot;&gt;-&lt;/span&gt; &lt;span class=&quot;na&quot;&gt;name&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;Setup Node.js with node_modules cache&lt;/span&gt;
  &lt;span class=&quot;na&quot;&gt;id&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;node-with-cache&lt;/span&gt;
  &lt;span class=&quot;na&quot;&gt;uses&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;magnetikonline/action-node-modules-cache@v2&lt;/span&gt;
  &lt;span class=&quot;na&quot;&gt;with&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt;
    &lt;span class=&quot;na&quot;&gt;node-version-file&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;.nvmrc&lt;/span&gt;
    &lt;span class=&quot;na&quot;&gt;additional-cache-path&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;pi&quot;&gt;|&lt;/span&gt;
      &lt;span class=&quot;s&quot;&gt;.cache&lt;/span&gt;
&lt;span class=&quot;pi&quot;&gt;-&lt;/span&gt; &lt;span class=&quot;na&quot;&gt;name&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;Install npm packages&lt;/span&gt;
  &lt;span class=&quot;na&quot;&gt;if&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;steps.node-with-cache.outputs.cache-hit != &#39;true&#39;&lt;/span&gt;
  &lt;span class=&quot;na&quot;&gt;run&lt;/span&gt;&lt;span class=&quot;pi&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;s&quot;&gt;npm ci&lt;/span&gt;
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h2 id=&quot;conclusion&quot;&gt;Conclusion&lt;/h2&gt;

&lt;p&gt;By enabling cache for Jest, Prettier, ESLint, and TypeScript, you can speed up your development workflow.
And you can also make your CI faster! 🚀&lt;/p&gt;

&lt;p&gt;Alternative tools exist, like &lt;a href=&quot;https://biomejs.dev/&quot;&gt;Biome&lt;/a&gt;, providing format and lint in the same tool, ultra-fast thanks to Rust, but quite limited in terms of linting rules for now.&lt;/p&gt; 

🔥 2024-06-15 
 [How to configure MSMTP for self-hosted email](https://www.tomsquest.com/blog/2024/06/configure-msmtp-selfhost/) 
 > &lt;p&gt;I recently reinstalled my server and configured &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;msmtp&lt;/code&gt; to send emails to myself. It wasn’t working right at first. Here’s how to configure it properly.&lt;/p&gt;

&lt;h2 id=&quot;tldr&quot;&gt;TL;DR&lt;/h2&gt;

&lt;ul&gt;
  &lt;li&gt;Set &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;allow_from_override&lt;/code&gt; to &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;off&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;Use an &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;aliases&lt;/code&gt; file with a &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;default&lt;/code&gt; alias&lt;/li&gt;
&lt;/ul&gt;

&lt;h2 id=&quot;msmtp&quot;&gt;MSMTP&lt;/h2&gt;

&lt;p&gt;MSMTP is the recommended tool to send emails from a server, much simpler than a full email solution like Postfix. It’s also actively maintained, unlike SSMTP which I used before.&lt;/p&gt;

&lt;p&gt;Install MSMTP with its &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;-mta&lt;/code&gt; version:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;apt &lt;span class=&quot;nb&quot;&gt;install &lt;/span&gt;msmtp-mta
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;I use the service of &lt;a href=&quot;https://www.mailjet.com/&quot;&gt;MailJet&lt;/a&gt; to send emails. It’s free for a few thousand emails per month. Working great for years!&lt;/p&gt;

&lt;h2 id=&quot;goal&quot;&gt;Goal&lt;/h2&gt;

&lt;ol&gt;
  &lt;li&gt;Allow services to send email to &lt;strong&gt;myself and only myself&lt;/strong&gt;.&lt;/li&gt;
  &lt;li&gt;Ensure MailJet accepts emails only if they originate &lt;strong&gt;from myself&lt;/strong&gt;.&lt;/li&gt;
&lt;/ol&gt;

&lt;p&gt;The &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;From&lt;/code&gt; field must be set to me. And a &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;default&lt;/code&gt; must be set.
Those were the main issues I had.&lt;/p&gt;

&lt;h2 id=&quot;configuration&quot;&gt;Configuration&lt;/h2&gt;

&lt;p&gt;The main points :&lt;/p&gt;
&lt;ul&gt;
  &lt;li&gt;Set &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;allow_from_override&lt;/code&gt; to &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;off&lt;/code&gt; so that smtp can set the &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;From&lt;/code&gt; field&lt;/li&gt;
  &lt;li&gt;Set a &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;From&lt;/code&gt; in MSMTP&lt;/li&gt;
  &lt;li&gt;&lt;strong&gt;AND&lt;/strong&gt; set an alias file &lpar;see below&rpar;&lt;/li&gt;
&lt;/ul&gt;

&lt;p&gt;Config in &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;/etc/msmtprc&lt;/code&gt;:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;account default
host &lt;span class=&quot;k&quot;&gt;in&lt;/span&gt;&lt;span class=&quot;nt&quot;&gt;-v3&lt;/span&gt;.mailjet.com
port 587
tls on
tls_starttls on
auth on
user 1234
password abcd
from tom@tomsquest.com
allow_from_override off
set_from_header on
aliases /etc/msmtprc_aliases
syslog LOG_MAIL
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;Aliases in &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;/etc/msmtprc_aliases&lt;/code&gt;:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;default: tom@tomsquest.com
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;This way, all conditions are handled:&lt;/p&gt;
&lt;ul&gt;
  &lt;li&gt;a service can set a &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;From&lt;/code&gt; field &lpar;&lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;root&lt;/code&gt;, &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;@tom&lt;/code&gt;…&rpar;&lt;/li&gt;
  &lt;li&gt;Or no &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;From&lt;/code&gt; field at all&lt;/li&gt;
&lt;/ul&gt;

&lt;h2 id=&quot;about-self-hosting&quot;&gt;About self-hosting&lt;/h2&gt;

&lt;p&gt;I self-host most of my services, such as:&lt;/p&gt;
&lt;ul&gt;
  &lt;li&gt;&lt;a href=&quot;https://github.com/tomsquest/docker-radicale/&quot;&gt;Calendar/Tasks using my custom Radicale image&lt;/a&gt;&lt;/li&gt;
  &lt;li&gt;qBittorent&lt;/li&gt;
  &lt;li&gt;SearchX&lt;/li&gt;
  &lt;li&gt;Backup with Restic&lt;/li&gt;
  &lt;li&gt;Synchronization with Syncthing&lt;/li&gt;
&lt;/ul&gt;

&lt;h3 id=&quot;self-hosting-is-an-excellent-way-to-learn&quot;&gt;Self-hosting is an excellent way to learn&lt;/h3&gt;

&lt;p&gt;There’s always a risk when you colocate your data with services made by others.&lt;/p&gt;

&lt;p&gt;That’s why I read a lot on Docker and created my own Docker image for the calendar server Radicale! I wanted it to not run as root, be read-only, and avoid pid1 issues.&lt;/p&gt;

&lt;h3 id=&quot;requires-a-bit-of-surveillance&quot;&gt;Requires a Bit of Surveillance&lt;/h3&gt;

&lt;p&gt;For instance, I receive backup emails twice a day and check each time which files were backed up. That’s part of my routine.&lt;/p&gt;

&lt;p&gt;I also monitor Crowdsec and update emails.&lt;/p&gt;

&lt;h3 id=&quot;cumbersome-every-few-years&quot;&gt;Cumbersome every few years&lt;/h3&gt;

&lt;p&gt;Yes, upgrades run automatically, but moving from one LTS version to another requires reinstalling. I’ve done it twice in 8 years.
No, I didn’t go the Ansible route &lpar;it requires testing and thus time&rpar;, and the same with NixOS.
I just have a partial documentation of high-level actions and backup my /etc files.&lt;/p&gt; 

✨ 2020-02-28 
 [Think carbon, reduce typescript bundle size](https://www.tomsquest.com/blog/2020/02/think-carbon-reduce-typescript-bundle-size/) 
 > &lt;p&gt;Reducing the size of JS compiled from Typescript is easy.&lt;/p&gt;

&lt;p&gt;Here are some numbers and how to do it. It’s nearly free if you run on Node &lpar;!= browser&rpar;.&lt;/p&gt;

&lt;h2 id=&quot;tldr&quot;&gt;TL;DR&lt;/h2&gt;

&lt;ul&gt;
  &lt;li&gt;Install &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;tslib&lt;/code&gt;&lt;/li&gt;
  &lt;li&gt;Configure &lt;code class=&quot;language-plaintext highlighter-rouge&quot;&gt;importHelpers&lt;/code&gt;&lt;/li&gt;
&lt;/ul&gt;

&lt;p&gt;= reduce JS file size = reduce storage and network = 🌿 &lt;strong&gt;Reduce carbon footprint&lt;/strong&gt;!&lt;/p&gt;

&lt;h2 id=&quot;size-matters&quot;&gt;Size matters&lt;/h2&gt;

&lt;p&gt;Sizes after and before, with the difference in percent:&lt;/p&gt;

&lt;table&gt;
  &lt;thead&gt;
    &lt;tr&gt;
      &lt;th&gt; &lt;/th&gt;
      &lt;th style=&quot;text-align: right&quot;&gt;Before&lt;/th&gt;
      &lt;th style=&quot;text-align: right&quot;&gt;After&lt;/th&gt;
      &lt;th style=&quot;text-align: right&quot;&gt;Diff&lt;/th&gt;
    &lt;/tr&gt;
  &lt;/thead&gt;
  &lt;tbody&gt;
    &lt;tr&gt;
      &lt;td&gt;line count *.js&lt;/td&gt;
      &lt;td style=&quot;text-align: right&quot;&gt;4397 lines&lt;/td&gt;
      &lt;td style=&quot;text-align: right&quot;&gt;4011 lines&lt;/td&gt;
      &lt;td style=&quot;text-align: right&quot;&gt;-9%&lt;/td&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
      &lt;td&gt;size in bytes *.js&lt;/td&gt;
      &lt;td style=&quot;text-align: right&quot;&gt;186k&lt;/td&gt;
      &lt;td style=&quot;text-align: right&quot;&gt;153k&lt;/td&gt;
      &lt;td style=&quot;text-align: right&quot;&gt;-18%&lt;/td&gt;
    &lt;/tr&gt;
  &lt;/tbody&gt;
&lt;/table&gt;

&lt;h2 id=&quot;how-to&quot;&gt;How-to&lt;/h2&gt;

&lt;p&gt;&lt;a href=&quot;https://github.com/microsoft/tslib&quot;&gt;Install tslib&lt;/a&gt;, the library that contains the helper code:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;npm &lt;span class=&quot;nb&quot;&gt;install &lt;/span&gt;tslib
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;Enable the import of the helper functions:&lt;/p&gt;

&lt;div class=&quot;language-json highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;compilerOptions&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
    &lt;/span&gt;&lt;span class=&quot;nl&quot;&gt;&quot;importHelpers&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt;&lt;span class=&quot;w&quot;&gt; &lt;/span&gt;&lt;span class=&quot;kc&quot;&gt;true&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
  &lt;/span&gt;&lt;span class=&quot;p&quot;&gt;}&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;}&lt;/span&gt;&lt;span class=&quot;w&quot;&gt;
&lt;/span&gt;&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h2 id=&quot;what-does-it-do&quot;&gt;What does it do&lt;/h2&gt;

&lt;p&gt;Typescript will create helper functions for decorators, extends, assign, etc. in each compiled file.&lt;/p&gt;

&lt;p&gt;But, Typescript can use a reference library, &lt;a href=&quot;https://github.com/microsoft/tslib&quot;&gt;tslib&lt;/a&gt; and import the functions from there.&lt;/p&gt;

&lt;p&gt;Sample of generated code without tslib/importHelpers:&lt;/p&gt;

&lt;div class=&quot;language-js highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;kd&quot;&gt;var&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;__decorate&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;k&quot;&gt;this&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;amp;&amp;amp;&lt;/span&gt; &lt;span class=&quot;k&quot;&gt;this&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;__decorate&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;||&lt;/span&gt; &lt;span class=&quot;kd&quot;&gt;function&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;decorators&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;target&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;key&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;desc&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;{&lt;/span&gt;
    &lt;span class=&quot;kd&quot;&gt;var&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;c&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;arguments&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;length&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;c&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;lt;&lt;/span&gt; &lt;span class=&quot;mi&quot;&gt;3&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;?&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;target&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;desc&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;===&lt;/span&gt; &lt;span class=&quot;kc&quot;&gt;null&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;?&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;desc&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;nb&quot;&gt;Object&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;getOwnPropertyDescriptor&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;target&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;key&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;desc&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;d&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;;&lt;/span&gt;
    &lt;span class=&quot;k&quot;&gt;if&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;k&quot;&gt;typeof&lt;/span&gt; &lt;span class=&quot;nb&quot;&gt;Reflect&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;===&lt;/span&gt; &lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;object&lt;/span&gt;&lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;amp;&amp;amp;&lt;/span&gt; &lt;span class=&quot;k&quot;&gt;typeof&lt;/span&gt; &lt;span class=&quot;nb&quot;&gt;Reflect&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;decorate&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;===&lt;/span&gt; &lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;function&lt;/span&gt;&lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;nb&quot;&gt;Reflect&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;decorate&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;decorators&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;target&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;key&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;desc&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;;&lt;/span&gt;
    &lt;span class=&quot;k&quot;&gt;else&lt;/span&gt; &lt;span class=&quot;k&quot;&gt;for&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;kd&quot;&gt;var&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;i&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;decorators&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;length&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;-&lt;/span&gt; &lt;span class=&quot;mi&quot;&gt;1&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;;&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;i&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;gt;=&lt;/span&gt; &lt;span class=&quot;mi&quot;&gt;0&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;;&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;i&lt;/span&gt;&lt;span class=&quot;o&quot;&gt;--&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt; &lt;span class=&quot;k&quot;&gt;if&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;d&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;decorators&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;[&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;i&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;]&rpar;&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;c&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;lt;&lt;/span&gt; &lt;span class=&quot;mi&quot;&gt;3&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;?&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;d&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;c&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;gt;&lt;/span&gt; &lt;span class=&quot;mi&quot;&gt;3&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;?&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;d&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;target&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;key&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;d&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;target&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;key&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;&rpar;&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;||&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;;&lt;/span&gt;
    &lt;span class=&quot;k&quot;&gt;return&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;c&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;gt;&lt;/span&gt; &lt;span class=&quot;mi&quot;&gt;3&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;amp;&amp;amp;&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&amp;amp;&amp;amp;&lt;/span&gt; &lt;span class=&quot;nb&quot;&gt;Object&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;defineProperty&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;target&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;key&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;,&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;r&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;;&lt;/span&gt;
&lt;span class=&quot;p&quot;&gt;};&lt;/span&gt;
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;p&gt;With tslib/importHelpers:&lt;/p&gt;

&lt;div class=&quot;language-js highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;use strict&lt;/span&gt;&lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;;&lt;/span&gt;
&lt;span class=&quot;nb&quot;&gt;Object&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;.&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;defineProperty&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;nx&quot;&gt;exports&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;__esModule&lt;/span&gt;&lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;,&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;{&lt;/span&gt; &lt;span class=&quot;na&quot;&gt;value&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;:&lt;/span&gt; &lt;span class=&quot;kc&quot;&gt;true&lt;/span&gt; &lt;span class=&quot;p&quot;&gt;}&rpar;;&lt;/span&gt;
&lt;span class=&quot;kd&quot;&gt;const&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;tslib_1&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;=&lt;/span&gt; &lt;span class=&quot;nx&quot;&gt;require&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&lpar;&lt;/span&gt;&lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;s2&quot;&gt;tslib&lt;/span&gt;&lt;span class=&quot;dl&quot;&gt;&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;&rpar;;&lt;/span&gt;
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt;

&lt;h2 id=&quot;measuring&quot;&gt;Measuring&lt;/h2&gt;

&lt;p&gt;I measured the size before and after with the following commands:&lt;/p&gt;

&lt;div class=&quot;language-bash highlighter-rouge&quot;&gt;&lt;div class=&quot;highlight&quot;&gt;&lt;pre class=&quot;highlight&quot;&gt;&lt;code&gt;&lt;span class=&quot;c&quot;&gt;# don&#39;t forget to do a clean-build&lt;/span&gt;
&lt;span class=&quot;c&quot;&gt;# npm run clean &amp;amp;&amp;amp; npm run build&lt;/span&gt;

&lt;span class=&quot;c&quot;&gt;# count lines&lt;/span&gt;
&lt;span class=&quot;o&quot;&gt;&lpar;&lt;/span&gt; find ./dist &lt;span class=&quot;nt&quot;&gt;-name&lt;/span&gt; &lt;span class=&quot;s1&quot;&gt;&#39;*.js&#39;&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;-print0&lt;/span&gt; | xargs &lt;span class=&quot;nt&quot;&gt;-0&lt;/span&gt; &lt;span class=&quot;nb&quot;&gt;cat&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;&rpar;&lt;/span&gt; | &lt;span class=&quot;nb&quot;&gt;wc&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;-l&lt;/span&gt;

&lt;span class=&quot;c&quot;&gt;# total size in bytes&lt;/span&gt;
&lt;span class=&quot;o&quot;&gt;{&lt;/span&gt; find dist &lt;span class=&quot;nt&quot;&gt;-type&lt;/span&gt; f &lt;span class=&quot;nt&quot;&gt;-name&lt;/span&gt; &lt;span class=&quot;s2&quot;&gt;&quot;*.js&quot;&lt;/span&gt; &lt;span class=&quot;nt&quot;&gt;-printf&lt;/span&gt; &lt;span class=&quot;s2&quot;&gt;&quot;%s+&quot;&lt;/span&gt;&lt;span class=&quot;p&quot;&gt;;&lt;/span&gt; &lt;span class=&quot;nb&quot;&gt;echo &lt;/span&gt;0&lt;span class=&quot;p&quot;&gt;;&lt;/span&gt; &lt;span class=&quot;o&quot;&gt;}&lt;/span&gt; | bc
&lt;/code&gt;&lt;/pre&gt;&lt;/div&gt;&lt;/div&gt; 
<!-- BLOG-POST-LIST:END -->