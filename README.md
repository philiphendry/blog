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
