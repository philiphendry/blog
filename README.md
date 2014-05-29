# Philip Hendry's Blog

My first blog was self-hosted WordPress, then I migrated to [WordPress](http://philiphendry.wordpress.com/) and I've also dabbled with SubText... but it's time or a change. Hosted WordPress is cheap and cheerful but it's restrictions were starting to get a little annoying... but I'm not going back to self-hosting, it's just too much trouble.

Hence the decision to bring things into GitHub which supports Jekyll. More importantly I can use my favourite new editing tool [StackEdit](http://stackedit.io) and publish in markdown directly to GitHub.

## Initial Setup

1. Create a GitHub repository called *blog*.
2. Add a branch called *gh-pages* - anything committed here is published to the GitHub website.
3. To seed the project with some style I went to the repository settings and clicked the *Automatic page generator*.
4. At this point I was also prompt to create a new Google Analytics project and assign the tracking id to the generated template.

## Assigning my domain name

1. Logged into 123-reg and added a CNAME record from blog.philiphendry.me.uk to philiphendry.github.io.
2. Added a file called CNAME in the route of the GitHub repository with blog.philiphendry.me.uk inside.

## Building a Blog with Jekyll

First [Install Jekyll](http://jekyllrb.com/docs/installation/)

Tried to following instructions on [Jekyll](http://jekyllrb.com/docs/quickstart/) to create and build my blog site but failed with error :

  	Configuration file: C:/Users/Philip/Documents/Jekyll/blog/_config.yml
  	            Source: C:/Users/Philip/Documents/Jekyll/blog
  	       Destination: C:/Users/Philip/Documents/Jekyll/blog/_site
  	      Generating...
  	C:/Tools/Ruby193/lib/ruby/gems/1.9.1/gems/posix-spawn-0.3.8/lib/posix/spawn.rb:162: warning: cannot close fd before spawn
  	'which' is not recognized as an internal or external command,
  	operable program or batch file.
  	←[31m  Liquid Exception: No such file or directory - python C:/Tools/Ruby193/lib/ruby/gems/1.9.1/gems/pygments.rb-0.5.4/lib/pygmen
  	ts/mentos.py in _posts/2014-05-28-welcome-to-jekyll.markdown←[0m
  	                    done.

Followed instructions at [StackOverflow](http://stackoverflow.com/questions/17364028/jekyll-on-windows-pygments-not-working) to regress version of Pygments and also installed Python due to the remaining error:

  	←[31m  Liquid Exception: No such file or directory - python C:/Tools/Ruby193/lib/ruby/gems/1.9.1/gems/pygments.rb-0.5.4/lib/pygmen
  	ts/mentos.py in _posts/2014-05-28-welcome-to-jekyll.markdown←[0m

Added Python to the PATH

Now getting the error :

  ←[31m  Liquid Exception: Failed to get header. in _posts/2014-05-28-welcome-to-jekyll.markdown←[0m
  jekyll 2.0.3 | Error:  Failed to get header.

According to [StackOverflow](http://stackoverflow.com/questions/17816499/jekyll-on-windows-liquid-exception-failed-to-get-header) this may be due to the version of python so uninstalled 3.4.1 and download 2.7.6

Ensured python was in the PATH, restarted command prompt, ran jekyll build - CLEAN BUILD :)

### Configure ExitWP for WordPress migration

1. Download repository
2. Install PyYaml
3. Install Python SetupTools for easy\_install pakage manager click [ez_setup.py](https://bootstrap.pypa.io/ez_setup.py) to download then run in the c:\tools\python27 folder.
4. Execute c:\tools\python27\scripts\easy_install.exe beautifulsoup4
5. Execute converter (python exitwp.py)
