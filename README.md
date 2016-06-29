Acodemy.io Github Page
============
http://10clouds.github.io/acodemy.io/

Setup
============
- Install `Jekyll` http://jekyllrb.com/
- run `jekyll serve`

Running project:
============
`jekyll serve -w --baseurl '/'`

Setup Jekyll on Windows: Troubleshooting
============
- http://yizeng.me/2013/05/10/setup-jekyll-on-windows/
- http://sourceforge.net/projects/unxutils/
- encoding problems when `jekyll serve`:
```
chcp 65001
jekyll serve
```
- pygments not working ?
```
  gem uninstall pygments.rb
  gem install pygments.rb --version "=0.5.0"
```
