---
Tags: [latex]
permalink: /post/rstudio.html
tags: [R,]
categories: [Notes]

---

## 说明
本页面的以下内容由RStudio生成的原始html页面，参考了阳志平老师的《[为什么Markdown+R有较大概率成为科技写作主流][1]》一文。

[1]: http://www.yangzhiping.com/tech/r-markdown-knitr.html

-----

<!DOCTYPE html>
<!-- saved from url=(0014)about:internet -->
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>

<title>The Normal Distribution</title>

<style type="text/css">
body, td {
   font-family: sans-serif;
   background-color: white;
   font-size: 12px;
   margin: 8px;
}

tt, code, pre {
   font-family: 'DejaVu Sans Mono', 'Droid Sans Mono', 'Lucida Console', Consolas, Monaco, monospace;
}

h1 { 
   font-size:2.2em; 
}

h2 { 
   font-size:1.8em; 
}

h3 { 
   font-size:1.4em; 
}

h4 { 
   font-size:1.0em; 
}

h5 { 
   font-size:0.9em; 
}

h6 { 
   font-size:0.8em; 
}

a:visited {
   color: rgb(50%, 0%, 50%);
}

pre {	
   margin-top: 0;
   max-width: 95%;
   border: 1px solid #ccc;
   white-space: pre-wrap;
}

pre code {
   display: block; padding: 0.5em;
}

code.r, code.cpp {
   background-color: #F8F8F8;
}

table, td, th {
  border: none;
}

blockquote {
   color:#666666;
   margin:0;
   padding-left: 1em;
   border-left: 0.5em #EEE solid;
}

hr {
   height: 0px;
   border-bottom: none;
   border-top-width: thin;
   border-top-style: dotted;
   border-top-color: #999999;
}

@media print {
   * { 
      background: transparent !important; 
      color: black !important; 
      filter:none !important; 
      -ms-filter: none !important; 
   }

   body { 
      font-size:12pt; 
      max-width:100%; 
   }
       
   a, a:visited { 
      text-decoration: underline; 
   }

   hr { 
      visibility: hidden;
      page-break-before: always;
   }

   pre, blockquote { 
      padding-right: 1em; 
      page-break-inside: avoid; 
   }

   tr, img { 
      page-break-inside: avoid; 
   }

   img { 
      max-width: 100% !important; 
   }

   @page :left { 
      margin: 15mm 20mm 15mm 10mm; 
   }
     
   @page :right { 
      margin: 15mm 10mm 15mm 20mm; 
   }

   p, h2, h3 { 
      orphans: 3; widows: 3; 
   }

   h2, h3 { 
      page-break-after: avoid; 
   }
}

</style>

<!-- Styles for R syntax highlighter -->
<style type="text/css">
   pre .operator,
   pre .paren {
     color: rgb(104, 118, 135)
   }

   pre .literal {
     color: rgb(88, 72, 246)
   }

   pre .number {
     color: rgb(0, 0, 205);
   }

   pre .comment {
     color: rgb(76, 136, 107);
   }

   pre .keyword {
     color: rgb(0, 0, 255);
   }

   pre .identifier {
     color: rgb(0, 0, 0);
   }

   pre .string {
     color: rgb(3, 106, 7);
   }
</style>

<!-- R syntax highlighter -->
<script type="text/javascript">
var hljs=new function(){function m(p){return p.replace(/&/gm,"&").replace(/</gm,"<")}function f(r,q,p){return RegExp(q,"m"+(r.cI?"i":"")+(p?"g":""))}function b(r){for(var p=0;p<r.childNodes.length;p++){var q=r.childNodes[p];if(q.nodeName=="CODE"){return q}if(!(q.nodeType==3&&q.nodeValue.match(/\s+/))){break}}}function h(t,s){var p="";for(var r=0;r<t.childNodes.length;r++){if(t.childNodes[r].nodeType==3){var q=t.childNodes[r].nodeValue;if(s){q=q.replace(/\n/g,"")}p+=q}else{if(t.childNodes[r].nodeName=="BR"){p+="\n"}else{p+=h(t.childNodes[r])}}}if(/MSIE [678]/.test(navigator.userAgent)){p=p.replace(/\r/g,"\n")}return p}function a(s){var r=s.className.split(/\s+/);r=r.concat(s.parentNode.className.split(/\s+/));for(var q=0;q<r.length;q++){var p=r[q].replace(/^language-/,"");if(e[p]){return p}}}function c(q){var p=[];(function(s,t){for(var r=0;r<s.childNodes.length;r++){if(s.childNodes[r].nodeType==3){t+=s.childNodes[r].nodeValue.length}else{if(s.childNodes[r].nodeName=="BR"){t+=1}else{if(s.childNodes[r].nodeType==1){p.push({event:"start",offset:t,node:s.childNodes[r]});t=arguments.callee(s.childNodes[r],t);p.push({event:"stop",offset:t,node:s.childNodes[r]})}}}}return t})(q,0);return p}function k(y,w,x){var q=0;var z="";var s=[];function u(){if(y.length&&w.length){if(y[0].offset!=w[0].offset){return(y[0].offset<w[0].offset)?y:w}else{return w[0].event=="start"?y:w}}else{return y.length?y:w}}function t(D){var A="<"+D.nodeName.toLowerCase();for(var B=0;B<D.attributes.length;B++){var C=D.attributes[B];A+=" "+C.nodeName.toLowerCase();if(C.value!==undefined&&C.value!==false&&C.value!==null){A+='="'+m(C.value)+'"'}}return A+">"}while(y.length||w.length){var v=u().splice(0,1)[0];z+=m(x.substr(q,v.offset-q));q=v.offset;if(v.event=="start"){z+=t(v.node);s.push(v.node)}else{if(v.event=="stop"){var p,r=s.length;do{r--;p=s[r];z+=("</"+p.nodeName.toLowerCase()+">")}while(p!=v.node);s.splice(r,1);while(r<s.length){z+=t(s[r]);r++}}}}return z+m(x.substr(q))}function j(){function q(x,y,v){if(x.compiled){return}var u;var s=[];if(x.k){x.lR=f(y,x.l||hljs.IR,true);for(var w in x.k){if(!x.k.hasOwnProperty(w)){continue}if(x.k[w] instanceof Object){u=x.k[w]}else{u=x.k;w="keyword"}for(var r in u){if(!u.hasOwnProperty(r)){continue}x.k[r]=[w,u[r]];s.push(r)}}}if(!v){if(x.bWK){x.b="\\b("+s.join("|")+")\\s"}x.bR=f(y,x.b?x.b:"\\B|\\b");if(!x.e&&!x.eW){x.e="\\B|\\b"}if(x.e){x.eR=f(y,x.e)}}if(x.i){x.iR=f(y,x.i)}if(x.r===undefined){x.r=1}if(!x.c){x.c=[]}x.compiled=true;for(var t=0;t<x.c.length;t++){if(x.c[t]=="self"){x.c[t]=x}q(x.c[t],y,false)}if(x.starts){q(x.starts,y,false)}}for(var p in e){if(!e.hasOwnProperty(p)){continue}q(e[p].dM,e[p],true)}}function d(B,C){if(!j.called){j();j.called=true}function q(r,M){for(var L=0;L<M.c.length;L++){if((M.c[L].bR.exec(r)||[null])[0]==r){return M.c[L]}}}function v(L,r){if(D[L].e&&D[L].eR.test(r)){return 1}if(D[L].eW){var M=v(L-1,r);return M?M+1:0}return 0}function w(r,L){return L.i&&L.iR.test(r)}function K(N,O){var M=[];for(var L=0;L<N.c.length;L++){M.push(N.c[L].b)}var r=D.length-1;do{if(D[r].e){M.push(D[r].e)}r--}while(D[r+1].eW);if(N.i){M.push(N.i)}return f(O,M.join("|"),true)}function p(M,L){var N=D[D.length-1];if(!N.t){N.t=K(N,E)}N.t.lastIndex=L;var r=N.t.exec(M);return r?[M.substr(L,r.index-L),r[0],false]:[M.substr(L),"",true]}function z(N,r){var L=E.cI?r[0].toLowerCase():r[0];var M=N.k[L];if(M&&M instanceof Array){return M}return false}function F(L,P){L=m(L);if(!P.k){return L}var r="";var O=0;P.lR.lastIndex=0;var M=P.lR.exec(L);while(M){r+=L.substr(O,M.index-O);var N=z(P,M);if(N){x+=N[1];r+='<span class="'+N[0]+'">'+M[0]+"</span>"}else{r+=M[0]}O=P.lR.lastIndex;M=P.lR.exec(L)}return r+L.substr(O,L.length-O)}function J(L,M){if(M.sL&&e[M.sL]){var r=d(M.sL,L);x+=r.keyword_count;return r.value}else{return F(L,M)}}function I(M,r){var L=M.cN?'<span class="'+M.cN+'">':"";if(M.rB){y+=L;M.buffer=""}else{if(M.eB){y+=m(r)+L;M.buffer=""}else{y+=L;M.buffer=r}}D.push(M);A+=M.r}function G(N,M,Q){var R=D[D.length-1];if(Q){y+=J(R.buffer+N,R);return false}var P=q(M,R);if(P){y+=J(R.buffer+N,R);I(P,M);return P.rB}var L=v(D.length-1,M);if(L){var O=R.cN?"</span>":"";if(R.rE){y+=J(R.buffer+N,R)+O}else{if(R.eE){y+=J(R.buffer+N,R)+O+m(M)}else{y+=J(R.buffer+N+M,R)+O}}while(L>1){O=D[D.length-2].cN?"</span>":"";y+=O;L--;D.length--}var r=D[D.length-1];D.length--;D[D.length-1].buffer="";if(r.starts){I(r.starts,"")}return R.rE}if(w(M,R)){throw"Illegal"}}var E=e[B];var D=[E.dM];var A=0;var x=0;var y="";try{var s,u=0;E.dM.buffer="";do{s=p(C,u);var t=G(s[0],s[1],s[2]);u+=s[0].length;if(!t){u+=s[1].length}}while(!s[2]);if(D.length>1){throw"Illegal"}return{r:A,keyword_count:x,value:y}}catch(H){if(H=="Illegal"){return{r:0,keyword_count:0,value:m(C)}}else{throw H}}}function g(t){var p={keyword_count:0,r:0,value:m(t)};var r=p;for(var q in e){if(!e.hasOwnProperty(q)){continue}var s=d(q,t);s.language=q;if(s.keyword_count+s.r>r.keyword_count+r.r){r=s}if(s.keyword_count+s.r>p.keyword_count+p.r){r=p;p=s}}if(r.language){p.second_best=r}return p}function i(r,q,p){if(q){r=r.replace(/^((<[^>]+>|\t)+)/gm,function(t,w,v,u){return w.replace(/\t/g,q)})}if(p){r=r.replace(/\n/g,"<br>")}return r}function n(t,w,r){var x=h(t,r);var v=a(t);var y,s;if(v){y=d(v,x)}else{return}var q=c(t);if(q.length){s=document.createElement("pre");s.innerHTML=y.value;y.value=k(q,c(s),x)}y.value=i(y.value,w,r);var u=t.className;if(!u.match("(\\s|^)(language-)?"+v+"(\\s|$)")){u=u?(u+" "+v):v}if(/MSIE [678]/.test(navigator.userAgent)&&t.tagName=="CODE"&&t.parentNode.tagName=="PRE"){s=t.parentNode;var p=document.createElement("div");p.innerHTML="<pre><code>"+y.value+"</code></pre>";t=p.firstChild.firstChild;p.firstChild.cN=s.cN;s.parentNode.replaceChild(p.firstChild,s)}else{t.innerHTML=y.value}t.className=u;t.result={language:v,kw:y.keyword_count,re:y.r};if(y.second_best){t.second_best={language:y.second_best.language,kw:y.second_best.keyword_count,re:y.second_best.r}}}function o(){if(o.called){return}o.called=true;var r=document.getElementsByTagName("pre");for(var p=0;p<r.length;p++){var q=b(r[p]);if(q){n(q,hljs.tabReplace)}}}function l(){if(window.addEventListener){window.addEventListener("DOMContentLoaded",o,false);window.addEventListener("load",o,false)}else{if(window.attachEvent){window.attachEvent("onload",o)}else{window.onload=o}}}var e={};this.LANGUAGES=e;this.highlight=d;this.highlightAuto=g;this.fixMarkup=i;this.highlightBlock=n;this.initHighlighting=o;this.initHighlightingOnLoad=l;this.IR="[a-zA-Z][a-zA-Z0-9_]*";this.UIR="[a-zA-Z_][a-zA-Z0-9_]*";this.NR="\\b\\d+(\\.\\d+)?";this.CNR="\\b(0[xX][a-fA-F0-9]+|(\\d+(\\.\\d*)?|\\.\\d+)([eE][-+]?\\d+)?)";this.BNR="\\b(0b[01]+)";this.RSR="!|!=|!==|%|%=|&|&&|&=|\\*|\\*=|\\+|\\+=|,|\\.|-|-=|/|/=|:|;|<|<<|<<=|<=|=|==|===|>|>=|>>|>>=|>>>|>>>=|\\?|\\[|\\{|\\(|\\^|\\^=|\\||\\|=|\\|\\||~";this.ER="(?![\\s\\S])";this.BE={b:"\\\\.",r:0};this.ASM={cN:"string",b:"'",e:"'",i:"\\n",c:[this.BE],r:0};this.QSM={cN:"string",b:'"',e:'"',i:"\\n",c:[this.BE],r:0};this.CLCM={cN:"comment",b:"//",e:"$"};this.CBLCLM={cN:"comment",b:"/\\*",e:"\\*/"};this.HCM={cN:"comment",b:"#",e:"$"};this.NM={cN:"number",b:this.NR,r:0};this.CNM={cN:"number",b:this.CNR,r:0};this.BNM={cN:"number",b:this.BNR,r:0};this.inherit=function(r,s){var p={};for(var q in r){p[q]=r[q]}if(s){for(var q in s){p[q]=s[q]}}return p}}();hljs.LANGUAGES.cpp=function(){var a={keyword:{"false":1,"int":1,"float":1,"while":1,"private":1,"char":1,"catch":1,"export":1,virtual:1,operator:2,sizeof:2,dynamic_cast:2,typedef:2,const_cast:2,"const":1,struct:1,"for":1,static_cast:2,union:1,namespace:1,unsigned:1,"long":1,"throw":1,"volatile":2,"static":1,"protected":1,bool:1,template:1,mutable:1,"if":1,"public":1,friend:2,"do":1,"return":1,"goto":1,auto:1,"void":2,"enum":1,"else":1,"break":1,"new":1,extern:1,using:1,"true":1,"class":1,asm:1,"case":1,typeid:1,"short":1,reinterpret_cast:2,"default":1,"double":1,register:1,explicit:1,signed:1,typename:1,"try":1,"this":1,"switch":1,"continue":1,wchar_t:1,inline:1,"delete":1,alignof:1,char16_t:1,char32_t:1,constexpr:1,decltype:1,noexcept:1,nullptr:1,static_assert:1,thread_local:1,restrict:1,_Bool:1,complex:1},built_in:{std:1,string:1,cin:1,cout:1,cerr:1,clog:1,stringstream:1,istringstream:1,ostringstream:1,auto_ptr:1,deque:1,list:1,queue:1,stack:1,vector:1,map:1,set:1,bitset:1,multiset:1,multimap:1,unordered_set:1,unordered_map:1,unordered_multiset:1,unordered_multimap:1,array:1,shared_ptr:1}};return{dM:{k:a,i:"</",c:[hljs.CLCM,hljs.CBLCLM,hljs.QSM,{cN:"string",b:"'\\\\?.",e:"'",i:"."},{cN:"number",b:"\\b(\\d+(\\.\\d*)?|\\.\\d+)(u|U|l|L|ul|UL|f|F)"},hljs.CNM,{cN:"preprocessor",b:"#",e:"$"},{cN:"stl_container",b:"\\b(deque|list|queue|stack|vector|map|set|bitset|multiset|multimap|unordered_map|unordered_set|unordered_multiset|unordered_multimap|array)\\s*<",e:">",k:a,r:10,c:["self"]}]}}}();hljs.LANGUAGES.r={dM:{c:[hljs.HCM,{cN:"number",b:"\\b0[xX][0-9a-fA-F]+[Li]?\\b",e:hljs.IMMEDIATE_RE,r:0},{cN:"number",b:"\\b\\d+(?:[eE][+\\-]?\\d*)?L\\b",e:hljs.IMMEDIATE_RE,r:0},{cN:"number",b:"\\b\\d+\\.(?!\\d)(?:i\\b)?",e:hljs.IMMEDIATE_RE,r:1},{cN:"number",b:"\\b\\d+(?:\\.\\d*)?(?:[eE][+\\-]?\\d*)?i?\\b",e:hljs.IMMEDIATE_RE,r:0},{cN:"number",b:"\\.\\d+(?:[eE][+\\-]?\\d*)?i?\\b",e:hljs.IMMEDIATE_RE,r:1},{cN:"keyword",b:"(?:tryCatch|library|setGeneric|setGroupGeneric)\\b",e:hljs.IMMEDIATE_RE,r:10},{cN:"keyword",b:"\\.\\.\\.",e:hljs.IMMEDIATE_RE,r:10},{cN:"keyword",b:"\\.\\.\\d+(?![\\w.])",e:hljs.IMMEDIATE_RE,r:10},{cN:"keyword",b:"\\b(?:function)",e:hljs.IMMEDIATE_RE,r:2},{cN:"keyword",b:"(?:if|in|break|next|repeat|else|for|return|switch|while|try|stop|warning|require|attach|detach|source|setMethod|setClass)\\b",e:hljs.IMMEDIATE_RE,r:1},{cN:"literal",b:"(?:NA|NA_integer_|NA_real_|NA_character_|NA_complex_)\\b",e:hljs.IMMEDIATE_RE,r:10},{cN:"literal",b:"(?:NULL|TRUE|FALSE|T|F|Inf|NaN)\\b",e:hljs.IMMEDIATE_RE,r:1},{cN:"identifier",b:"[a-zA-Z.][a-zA-Z0-9._]*\\b",e:hljs.IMMEDIATE_RE,r:0},{cN:"operator",b:"<\\-(?!\\s*\\d)",e:hljs.IMMEDIATE_RE,r:2},{cN:"operator",b:"\\->|<\\-",e:hljs.IMMEDIATE_RE,r:1},{cN:"operator",b:"%%|~",e:hljs.IMMEDIATE_RE},{cN:"operator",b:">=|<=|==|!=|\\|\\||&&|=|\\+|\\-|\\*|/|\\^|>|<|!|&|\\||\\$|:",e:hljs.IMMEDIATE_RE,r:0},{cN:"operator",b:"%",e:"%",i:"\\n",r:1},{cN:"identifier",b:"`",e:"`",r:0},{cN:"string",b:'"',e:'"',c:[hljs.BE],r:0},{cN:"string",b:"'",e:"'",c:[hljs.BE],r:0},{cN:"paren",b:"[[({\\])}]",e:hljs.IMMEDIATE_RE,r:0}]}};
hljs.initHighlightingOnLoad();
</script>


<!-- MathJax scripts -->
<script type="text/javascript" src="https://c328740.ssl.cf1.rackcdn.com/mathjax/2.0-latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>



</head>

<body>
<h2>The Normal Distribution</h2>

<p>The normal distribution is defined as follows:</p>

<p>\[ f(x;\mu,\sigma^2) = \frac{1}{\sigma\sqrt{2\pi}} e^{ -\frac{1}{2}\left(\frac{x-\mu}{\sigma}\right)^2 }
 \]</p>

<p>To generate random draws from a normal distribution we use the <strong>rnorm</strong> function:</p>

<pre><code class="r">output <- rnorm(1000, 100, 15)
</code></pre>

<p>The normal distribution has the typical bell shape:</p>

<pre><code class="r">ggplot2::qplot(output)
</code></pre>

<pre><code>## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust
## this.
</code></pre>

<p><img src="data:image/png;base64,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YVGqh5UUScBXAKSDnfUMFz3spYOY9fCVHs4iIYAAAggggAACNgj4ilr27NkjkydPNoHPtGnTRE9/fvzxx20oL3lEAAEEEEAAAQTEVwB0zz33mNtW6DggvUih3ttLT43fvXs3pAgggAACCCCAQOQFfB0C00BHjwV79wH70Ic+JHfddZds2bJF9BR2EgIIIIAAAgggEGUBXz1AOuB5x44dWeX64x//KHqNHxICCCCAAAIIIBB1AV89QEuXLjW3vJg1a5ZceumlpjdIr+Y8b968qJeX/CGAAAIIIIAAAv7GAOnVnvWqz1OnTjWHwZYvXy4vvfQSFzCkQSGAAAIIIICAFQK+eoD0BmbPPfecPPjgg5JMJuXee+811wHSM8JICCCAAAIIIIBA1AV8jQHSs8BOnjxprgWkBbz++uvltttuM2eERb3A5A8BBBBAAAEEEPDVA7R161Z56623JJFIGEENfvTMsHXr1snChQtRRQABBBBAAAEEIi3gqweoqqpKtm3bllWwzZs3S3V1ddZnvEEAAQQQQAABBKIo4KsH6KGHHpLZs2fLlClTZPTo0bJp0yapqanhLLAo1jB5QgABBBBAAIELBHwFQPPnz5c///nP5sKHjY2NZjD0ZZdddsHC+QABBBBAAAEEEIiigK8ASAsyfvx484hiocgTAggggAACCCDQk4DvAKinhfIdAggggED8BOrq6uJXKErkrICvQdDOalFwBBBAAAEEEIiFAAFQLKqRQiCAAAIIIIBALgIEQLloMS0CCCCAAAIIxEKAACgW1UghEEAAAQQQQCAXAQKgXLSYFgEEEEAAAQRiIUAAFItqpBAIIIAAAgggkIsAAVAuWkyLAAIIIIAAArEQIACKRTVSCAQQQAABBBDIRYAAKBctpkUAAQQQQACBWAgQAMWiGikEAggggAACCOQiQACUixbTIoAAAggggEAsBAiAYlGNFAIBBBBAAAEEchEgAMpFi2kRQAABBBBAIBYCBECxqEYKgQACCCCAAAK5CKRymThK05aWloo+opJSqZRUVFREJTuh5EMNksmk8w4lJSWiFp2dnaHUAytFIE4CcdquJhIJ0e2DPlxOup/QFHbdWhsAtba2Snt7e2TakFZkU1NTZPITRkY0INWGjUOpaZsdHR1hVAPrRCBWAnHannj/HLW0tMSqjnItTP/+/UX34UHUbVVVVa6r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" alt="plot of chunk block2"/> </p>

</body>

</html>

