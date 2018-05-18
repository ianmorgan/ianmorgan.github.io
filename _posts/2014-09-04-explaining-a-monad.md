---
layout: post
title:  "Explaining a Monad"
date:   2014-09-04 16:16:01 +0000
categories: jekyll update
---
                
                <h1 id="explaining-a-monad-in-10-steps">Explaining a monad in 10 steps</h1>

                <p>wibble</p>

                <p>I thought i’d try and explain a monad, partly for the selfish reason that it would force me
                    understand them better myself. Example code is in Scala.</p>

                <h2 id="step-1---categories-are-containers">Step 1 - Categories are Containers</h2>

                <p>Monads come from Category Theory. Categories are just containers with a set of common behaviours
                    that should be true for all types and values. Of course formally proving they work for anything may be
                    difficult, but in the real world we can normally rely upon intuition and common sense to decide what
                    is good category. A list is a classic example.</p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span> <span class="o">&gt;</span> <span class="k">val</span> <span class="n">names</span> <span class="k">=</span> <span class="nc">List</span><span class="o">(</span><span class="s">&quot;John&quot;</span><span class="o">,</span> <span class="s">&quot;Paul&quot;</span><span class="o">,</span> <span class="s">&quot;Ringo&quot;</span><span class="o">,</span> <span class="s">&quot;George&quot;</span><span class="o">)</span>
<span class="n">names</span><span class="k">:</span> <span class="kt">List</span><span class="o">[</span><span class="kt">java.lang.String</span><span class="o">]</span> <span class="k">=</span> <span class="nc">List</span><span class="o">(</span><span class="nc">John</span><span class="o">,</span> <span class="nc">Paul</span><span class="o">,</span> <span class="nc">Ringo</span><span class="o">,</span> <span class="nc">George</span><span class="o">)</span>

<span class="n">scala</span><span class="o">&gt;</span> <span class="n">names</span><span class="o">.</span><span class="n">size</span>
<span class="n">res0</span><span class="k">:</span> <span class="kt">Int</span> <span class="o">=</span> <span class="mi">4</span></code></pre></div>

                <h2 id="step-2---changing-data-in-our-container">Step 2 - Changing data in our container</h2>

                <p>Our containers are values, that is they are immutable and so they can’t be changed directly. And so
                    to manipulate them we need to create a new container based on the old ones. Converting from one value to another
                    is a pure function:</p>

                <pre>
f: X -&gt; Y
</pre>

                <p>The container needs to provide its clients a way to pass in the function. In Scala this is by
                    convention called the <code>map</code> method. </p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="n">names</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="k">_</span><span class="o">.</span><span class="n">toUpperCase</span><span class="o">)</span>
<span class="n">res1</span><span class="k">:</span> <span class="kt">List</span><span class="o">[</span><span class="kt">java.lang.String</span><span class="o">]</span> <span class="k">=</span> <span class="nc">List</span><span class="o">(</span><span class="nc">JOHN</span><span class="o">,</span> <span class="nc">PAUL</span><span class="o">,</span> <span class="nc">RINGO</span><span class="o">,</span> <span class="nc">GEORGE</span><span class="o">)</span></code></pre></div>

                <h2 id="step-3---composing-small-changes-together">Step 3 - Composing small changes together</h2>

                <p>Pure functions will compose in a repeatable way, so that a more complex function can be built. </p>

                <pre>
f: X -&gt; Y
g: Y -&gt; Z
</pre>

                <p>is the same as </p>

                <pre>
h: X -&gt; Z
</pre>

                <p>Which can also be drawn something like:</p>

                <p><img src="http://upload.wikimedia.org/wikipedia/commons/1/1a/MorphismComposition-01.png" /></p>

                <div class="footnote">
                    <a href="http://commons.wikimedia.org/wiki/File:Commutative_diagram_for_morphism.svg#mediaviewer/File:Commutative_diagram_for_morphism.svg">Commutative diagram for morphism</a>" by <a href="//commons.wikimedia.org/wiki/User:Cepheus" title="User:Cepheus">User:Cepheus</a> - Own work, based on <a href="//en.wikipedia.org/wiki/Image:MorphismComposition-01.png" class="extiw" title="en:Image:MorphismComposition-01.png">en:Image:MorphismComposition-01.png</a>. Licensed under Public domain via <a href="//commons.wikimedia.org/wiki/">Wikimedia Commons</a>.
                </div>

                <p>The same is true of the map method. They may be chained together.</p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="n">names</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="k">_</span><span class="o">.</span><span class="n">toUpperCase</span><span class="o">).</span><span class="n">map</span><span class="o">(</span><span class="k">_</span><span class="o">.</span><span class="n">reverse</span><span class="o">)</span>
<span class="n">res2</span><span class="k">:</span> <span class="kt">List</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="nc">List</span><span class="o">(</span><span class="nc">NHOJ</span><span class="o">,</span> <span class="nc">LUAP</span><span class="o">,</span> <span class="nc">OGNIR</span><span class="o">,</span> <span class="nc">EGROEG</span><span class="o">)</span></code></pre></div>

                <h2 id="step-4---what-happens-if-my-function-creates-a-new-category">Step 4 - What happens if my function creates a new category</h2>

                <p>So far we have used functions that just return simple types. But what if the function returned something
                    more complex?</p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="k">val</span> <span class="n">names</span> <span class="k">=</span> <span class="nc">List</span><span class="o">(</span><span class="s">&quot;John L&quot;</span><span class="o">,</span><span class="s">&quot;Paul M&quot;</span><span class="o">)</span>
<span class="n">names</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="k">_</span><span class="o">.</span><span class="n">split</span><span class="o">(</span><span class="s">&quot; &quot;</span><span class="o">))</span>
<span class="n">res3</span><span class="k">:</span> <span class="kt">List</span><span class="o">[</span><span class="kt">Array</span><span class="o">[</span><span class="kt">java.lang.String</span><span class="o">]]</span> <span class="k">=</span> <span class="nc">List</span><span class="o">(</span><span class="nc">Array</span><span class="o">(</span><span class="nc">John</span><span class="o">,</span> <span class="n">L</span><span class="o">),</span> <span class="nc">Array</span><span class="o">(</span><span class="nc">Paul</span><span class="o">,</span> <span class="n">M</span><span class="o">)</span></code></pre></div>

                <p>So this returns a list of lists. </p>

                <p>To make it simpler, it can be flattened out into one list</p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="n">names</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="k">_</span><span class="o">.</span><span class="n">split</span><span class="o">(</span><span class="s">&quot; &quot;</span><span class="o">)).</span><span class="n">flatten</span>
<span class="n">res4</span><span class="k">:</span> <span class="kt">List</span><span class="o">[</span><span class="kt">java.lang.String</span><span class="o">]</span> <span class="k">=</span> <span class="nc">List</span><span class="o">(</span><span class="nc">John</span><span class="o">,</span> <span class="n">L</span><span class="o">,</span> <span class="nc">Paul</span><span class="o">,</span> <span class="n">M</span><span class="o">)</span></code></pre></div>

                <p>This is such a common pattern it can be combined into a single method, <code> flatMap </code></p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="n">names</span><span class="o">.</span><span class="n">flatMap</span><span class="o">(</span><span class="k">_</span><span class="o">.</span><span class="n">split</span><span class="o">(</span><span class="s">&quot; &quot;</span><span class="o">))</span>
<span class="n">res5</span><span class="k">:</span> <span class="kt">List</span><span class="o">[</span><span class="kt">java.lang.String</span><span class="o">]</span> <span class="k">=</span> <span class="nc">List</span><span class="o">(</span><span class="nc">John</span><span class="o">,</span> <span class="n">L</span><span class="o">,</span> <span class="nc">Paul</span><span class="o">,</span> <span class="n">M</span><span class="o">)</span></code></pre></div>

                <h2 id="step-5-that-was-a-monad-so-whats-special-about-it">Step 5: That was a monad, so whats special about it?</h2>

                <p>So, <code>flatMap</code> is fact a monad. The name is a little arbitrary. In Haskell its called bind, which is the name used in
                    category theory. The clever thing is thats its joined two containers into. In this example its two lists
                    and they are simply concatenated (flattened) together. Strictly speaking only <code>flatMap</code> must be defined as
                    <code>map</code> is really just a special case of flatMap with a single value. But for ease of use both are usually provided.</p>

                <p>There are two really useful things here.</p>

                <p>Firstly, cardinality (does the function return zero, one or multiple values)
                    becomes much less important which make designs more flexible. A similarity is joins in SQL, the syntax stays the same
                    regardless as to how many joins exist. In fact, the traditional imperative style of a variable for a single value,
                    a null for something that doesn’t exist and a container that must be manually unpacked for multiple values is often
                    unnecessarily cumbersome. </p>

                <p>Secondly many common problems can be modelled as a Category (aka container) with common behaviours. These include (using Scala’s names) <code>Option</code>
                    (holder for value that may be null) , a <code>Try</code> (holder for a computation that may have resulted in an exception)
                    and <code>Future</code> (holder for result of an asynchronous computation)</p>

                <h2 id="step-6-using-an-option-monad">Step 6: Using an Option monad</h2>

                <p>In Scala (and Java 8) the Option can be used to wrap a result that may be null, for example it could not be found in the database.
                    Option has methods to read the value and so on. It also provides <code>map</code> and <code>flatMap</code>. So in a suitably trivial example,
                    we might have a function that returns user names from a database:</p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="k">def</span> <span class="n">findName</span> <span class="o">(</span><span class="n">id</span><span class="k">:</span><span class="kt">Integer</span><span class="o">)</span> <span class="k">:</span> <span class="kt">Option</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="k">if</span> <span class="o">(</span><span class="n">id</span> <span class="o">==</span> <span class="mi">1</span><span class="o">)</span> <span class="nc">Some</span><span class="o">(</span><span class="s">&quot;John&quot;</span><span class="o">)</span> <span class="k">else</span> <span class="nc">None</span>
<span class="n">res6</span><span class="k">:</span> <span class="kt">findName:</span> <span class="o">(</span><span class="kt">id:</span> <span class="kt">Integer</span><span class="o">)</span><span class="kt">Option</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span></code></pre></div>

                <p>and another to get an address if we know the name</p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="k">def</span> <span class="n">findAddress</span> <span class="o">(</span><span class="n">name</span><span class="k">:</span><span class="kt">String</span><span class="o">)</span> <span class="k">:</span> <span class="kt">Option</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="k">if</span> <span class="o">(</span><span class="n">name</span><span class="o">.</span><span class="n">equals</span><span class="o">(</span><span class="s">&quot;John&quot;</span><span class="o">))</span> <span class="nc">Some</span><span class="o">(</span><span class="s">&quot;Liverpool&quot;</span><span class="o">)</span> <span class="k">else</span> <span class="nc">None</span>
<span class="n">res7</span><span class="k">:</span> <span class="kt">findAddress:</span> <span class="o">(</span><span class="kt">name:</span> <span class="kt">String</span><span class="o">)</span><span class="kt">Option</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span></code></pre></div>

                <p>These can now be safely chained together without worrying about null pointers</p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="n">findName</span><span class="o">(</span><span class="mi">1</span><span class="o">).</span><span class="n">map</span><span class="o">(</span><span class="n">findAddress</span><span class="o">(</span><span class="k">_</span><span class="o">))</span>
<span class="n">res8</span><span class="k">:</span> <span class="kt">Option</span><span class="o">[</span><span class="kt">Option</span><span class="o">[</span><span class="kt">String</span><span class="o">]]</span> <span class="k">=</span> <span class="nc">Some</span><span class="o">(</span><span class="nc">Some</span><span class="o">(</span><span class="nc">Liverpool</span><span class="o">))</span>
<span class="n">scala</span><span class="o">&gt;</span> <span class="n">findName</span><span class="o">(</span><span class="mi">2</span><span class="o">).</span><span class="n">map</span><span class="o">(</span><span class="n">findAddress</span><span class="o">(</span><span class="k">_</span><span class="o">))</span>
<span class="n">res9</span><span class="k">:</span> <span class="kt">Option</span><span class="o">[</span><span class="kt">Option</span><span class="o">[</span><span class="kt">String</span><span class="o">]]</span> <span class="k">=</span> <span class="nc">None</span></code></pre></div>

                <p>And <code>flatMap</code> will get rid the Option of Option complexity. The logic here is simple. If the container
                    (i.e. the instance the flatMap is called on) is None, then
                    it must be a None. If not, the function is applied and its result returned.</p>

                <div class="highlight"><pre><code class="language-scala" data-lang="scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="n">findName</span><span class="o">(</span><span class="mi">1</span><span class="o">).</span><span class="n bold">flatMap</span><span class="o">(</span><span class="n">findAddress</span><span class="o">(</span><span class="k">_</span><span class="o">))</span>
<span class="n">res8</span><span class="k">:</span> <span class="kt">Option</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="nc">Some</span><span class="o">(</span><span class="nc">Liverpool</span><span class="o">)</span>
<span class="n">scala</span><span class="o">&gt;</span> <span class="n">findName</span><span class="o">(</span><span class="mi">2</span><span class="o">).</span><span class="n bold">flatMap</span><span class="o">(</span><span class="n">findAddress</span><span class="o">(</span><span class="k">_</span><span class="o">))</span>
<span class="n">res9</span><span class="k">:</span> <span class="kt">Option</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="nc">None</span></code></pre></div>

                <p>Of course in this little example the effect seems a bit contrived but hopefully the benefit in more complex real world
                    scenario is clear. In Scala the <code>Option</code> type has a number of other useful tricks, one of which is to be
                    able to think of itself as a collection with either one value or empty. <a href="http://danielwestheide.com/blog/2012/12/19/the-neophytes-guide-to-scala-part-5-the-option-type.html">Here</a> is
                    good guide.</p>
