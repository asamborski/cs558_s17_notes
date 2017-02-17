# CS 558 Network Security 
## Scribe Notes Spring 2017

To post scribe notes, go to the `_posts` folder and create a pull request.

This can easily be done from the browser by going to the [_posts](_posts/) folder and clicking `Create new file` in the upper right.

### Instructions
1. Name your file `YYYY-MM-DD-title.md` for example `2017-02-15-RSA.md`
2. Write your notes in github flavored markdown. See this [cheetsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).
3. Include at the top of the file the following front matter:

	```
	---
	layout: notes
	title: RSA Security 
	scribe: Your Name
	---

	Notes go here...
	```

4. When you're done write a commit message and click `Propose new file`
5. Drop a star for your favorite TA 😎

See an example [here](https://raw.githubusercontent.com/asamborski/cs558_s17_notes/master/_posts/2017-01-19-Logistics.md)

### Including Images

Inline images can be easily included

Instructions:

1. Upload image to `img/` folder
2. Reference image in markdown 

```
![alt text]({ site.url }}/img/name_of_img.jpg "Title")
```


### Preview files locally

If you want to preview your notes locally you'll need to install [jekyll](https://jekyllrb.com/)

Then just run:

	jekyll serve
