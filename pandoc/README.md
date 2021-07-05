# Pandoc conversion notes
# modified for test commit
## 1. Pandoc conversion command

Edit ```data-dir``` in ```pandoc/options.yml```. Something like:
```data-dir: /Users/<your_username>/Library/Python/3.8/bin```

To create ```MBML.html``` form ```MBML.tex``` using pandoc:

```pandoc --defaults pandoc/options.yml```

You can compare your ```MBML.html``` file to:
```MBML_20210701.html```

## 2. Problems with the pandoc conversion --from latex --to html5

### 2.1 Front matter

1. **Appendix** chapters are numbered instead of lettered.
2. **List Of Figures** and **List Of Tables** are missing
   - Is it possible to get these via pandoc?

### 2.2 Formatting features

3. General:
   - ```<h5>```  headings are too small

4. ```chapsumm``` and ```lookbox``` ```<div>``` classes
   - The coloured boxes are too wide
   - Titles are currently just bold, they should look more like unnumbered headings (```<h3>``` or ```<h4>``` - whichever looks better).
   - Titles should be ```\definecolor{muteddkblue}{RGB}{53, 111, 155}```, as defined in ```00_FrontMatter/PackagesMacros.tex```. I can change the latex so that in the html, the title appears in a <span> instead. Would this help?
   - Note:
     ```\begin{chapsumm}``` in LaTeX, translates to ```<div class="chapsumm">``` in html. Similarly for ```lookbox```
   - Possible to fix these by editing ```pandoc/css/tweak.css```?
   - Would the titles be easier to fix if they were in a ```<span>``` inside the ```<div>```? I could change the LaTeX to get this.

### 2.3 Figure, Table and Code Listing formatting

5. Tables:
   - Too wide, I'd like them to take up only the space needed and overflow to the right side
   - Vertical and internal horizontal lines don't render in tables (there's probably a filter for this one?)
   - Sidenotes would be good instead of tablenotes in the html. See Table 1.1 for example. I used a [pandoc-sidenote](https://github.com/jez/pandoc-sidenote) to turn footnotes into sidenotes. Could perhaps do something similar for table notes?
6. Figures:
   - Should be centered in the main text
   - Have weirdly placed captions, these should be centered below the figure
   - Figures 2.4 and 2.5 should take up the full width (including the margin- like the tables currently do)
7. Code listings:
   - Have missing captions above. See code listing 3.1 for example.
   ```\begin{lstlisting}[caption={Calculating independence metrics for the data using AIF360}, label=clt_AIF360metric]  ```
    in LaTeX translates to
    ```<pre id="clt_AIF360metric" data-caption="Calculating independence metrics for the data using AIF360" label="clt_AIF360metric">```
    in html.

### 2.4 Labels and references to them

8. Figures, tables and code listings are not labelled with numbered identifiers, the references to them are linked but there is no label.
   - ```\label{}``` in the LaTeX doesn't work but ```\ref{}``` does for figures and tables
   - ```\label{label-name}``` after ```\caption{caption-text}``` in LaTeX translates to ```<div id="label-name">```
     ```\ref{label-name}``` in LaTeX translates to
     ```<a href="#label-name" data-reference-type="ref" data-reference="label-name">ch#.fig#</a>``` in html or in the case of a table
     ```<a href="#label-name" data-reference-type="ref" data-reference="label-name">ch#.tab#</a>``` instead.
   - **NB1:** Both ```\label{}``` and ```\ref{}``` (in the LaTeX) work after
     ```\chapter{name}``` / ```\section{name}``` / ```\subsection{name}``` / ```subsubsection{name}``` / ```paragraph{name}```... numbering. For example, 
     ```chapter{ch-name}\label{label-name}``` in LaTeX translates to
     ```<h1 data-number="value" id="label-name"><span class="header-section-number">value</span> name</h1>``` in html. It might be possible to fix this by editing the template ```tufte/tufte.html5```?
   - **NB2:** There may be a pandoc filter that can fix this? I tried a couple, see commented out filters in ```pandoc/options.yml```.

9. ```\label{}``` and ```\ref{}``` don't work on equations

   - The code
     ```latex
     \begin{equation}\label{eqn_name}
     latex_equation_code
     \end{equation}
     ```
     in LaTeX translates to 
     ```<span class="math display">\label{eqn_name}latex_equation_code</span>``` and ```\ref{eqn_name}``` translates to
     ```<a href="#eqn_name" data-reference-type="ref" data-reference="eqn_name">[eqn_name]</a>```which renders as ```[eqn_name]``` inline.
