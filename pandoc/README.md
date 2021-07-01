# Pandoc conversion notes

## 1. Pandoc conversion command

Edit ```data-dir``` in ```pandoc/options.yml```. Something like:
```data-dir: /Users/<your_username>/Library/Python/3.8/bin```

To convert ```MBML.tex``` to ```MBML.html```:

```pandoc --defaults pandoc/options.yml```

There is a file I created that you can compare your conversion to:
```MBML_20210630.html```

## 2. Problems with the pandoc conversion --from latex --to html5

### 2.1 Front matter

1. **Table Of Contents:** Appendix chapters are numbered instead of lettered
2. **List Of Figures:** missing - doubt it's possible to get this through pandoc
3. **List Of Tables:** missing - doubt it's possible to get this through pandoc

### 2.2 Formatting features

#### Chapter 1

4. ```chapsumm``` and ```lookbox``` environments need fixing
   - Titles are the wrong colour.
     Titles should be ```\definecolor{muteddkblue}{RGB}{53, 111, 155}```, as defined in ```00_FrontMatter/PackagesMacros.tex```. I can change the latex so that in the html, the title appears in a <span> instead. Would this help?
   - Note:
     ```\begin{chapsumm}``` in LaTeX, translates to ```<div class="chapsumm">``` in html. Similarly for ```lookbox```
   - Possible to fix this by editing ```pandoc/css/tweak.css```?

### 2.3 Labels and references to them

#### Chapter 1

5. ```\label{}``` doesn't work but ```\ref{}``` does for figures and tables
   - ```\label{label-name}``` after ```\caption{caption-text}``` in LaTeX translates to ```<div id="label-name">```
     ```\ref{label-name}``` in LaTeX translates to
     ```<a href="#label-name" data-reference-type="ref" data-reference="label-name">ch#.fig#</a>``` in html or in the case of a table
     ```<a href="#label-name" data-reference-type="ref" data-reference="label-name">ch#.tab#</a>``` instead.
   - **NB1:** Both ```\label{}``` and ```\ref{}``` (in the LaTeX) work after
     ```\chapter{name}``` / ```\section{name}``` / ```\subsection{name}``` / ```subsubsection{name}``` / ```paragraph{name}```... numbering. For example, 
     ```chapter{ch-name}\label{label-name}``` in LaTeX translates to
     ```<h1 data-number="value" id="label-name"><span class="header-section-number">value</span> name</h1>``` in html. It might be possible to fix this by editing the template ```tufte/tufte.html5```?
   - **NB2:** There may be a pandoc filter that can fix this? I tried a couple
     see commented out filters in ```pandoc/options.yml```

#### Chapter 3

6. ```\label{}``` and ```\ref{}``` don't work on equations

   - The code
     ```latex
     \begin{equation}\label{eqn_name}
     latex_equation_code
     \end{equation}
     ```
     in LaTeX translates to 
     ```<span class="math display">\label{eqn_name}latex_equation_code</span>``` and ```\ref{eqn_name}``` translates to
     ```<a href="#eqn_name" data-reference-type="ref" data-reference="eqn_name">[eqn_name]</a>```which renders as ```[eqn_name]``` inline.

### 2.4 Figures, Tables and CodeListings

#### Chapter 1

7. Vertical and internal horizontal lines don't render
   - there's probably a filter for this one, or it might be possible to write one?

8. Sidenotes would be good instead of tablenotes in the html
   - Currently using a [pandoc filter](https://github.com/jez/pandoc-sidenote) to fix this.

#### Chapter 3

9. Code listings do not have captions
   - ```\begin{lstlisting}[caption={Calculating independence metrics for the data using AIF360}, label=clt_AIF360metric]  ```
    in LaTeX translates to
    ```<pre id="clt_AIF360metric" data-caption="Calculating independence metrics for the data using AIF360" label="clt_AIF360metric">```
    in html
    