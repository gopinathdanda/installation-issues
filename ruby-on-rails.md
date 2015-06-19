#Ruby-on-Rails 

##Bundle Install Error in Mac OSX 10.10 Yosemite

While creating a ruby-on-rails project, I was having problem with <pre>bundle install</pre> The error I was getting was this:

<pre>
Building native extensions.  This could take a while...
ERROR:  Error installing json:
	ERROR: Failed to build gem native extension.

    /Users/gdanda/.rbenv/versions/2.0.0-p451/bin/ruby -r ./siteconf20150619-33014-1jndbkl.rb extconf.rb
creating Makefile

make "DESTDIR=" clean

make "DESTDIR="
compiling generator.c
linking shared-object json/ext/generator.bundle

make "DESTDIR=" install
make: /opt/local/bin/gmkdir: No such file or directory
make: *** [.RUBYARCHDIR.time] Error 1

make install failed, exit code 2

Gem files will remain installed in /Users/gdanda/.rbenv/versions/2.0.0-p451/lib/ruby/gems/2.0.0/gems/json-1.8.3 for inspection.
Results logged to /Users/gdanda/.rbenv/versions/2.0.0-p451/lib/ruby/gems/2.0.0/extensions/x86_64-darwin-13/2.0.0-static/json-1.8.3/gem_make.out
</pre>

###Resolution

As you can see, the problem was:

<pre>
make: /opt/local/bin/gmkdir: No such file or directory
</pre>

It turns out <strong>gmkdir</strong> is a part of <strong>coreutils</strong>. So I installed it using Homebrew:

<pre>
brew install coreutils
</pre>

But the directory was installed somewhere else and needed linking to the location the bundler was looking in:

<pre>
sudo ln -s /usr/local/bin/gmkdir /opt/local/bin/gmkdir
</pre>

But the problem continued:

<pre>
Building native extensions.  This could take a while...
ERROR:  Error installing json:
	ERROR: Failed to build gem native extension.

    /Users/gdanda/.rbenv/versions/2.0.0-p451/bin/ruby -r ./siteconf20150619-33181-14jdpub.rb extconf.rb
creating Makefile

make "DESTDIR=" clean

make "DESTDIR="
compiling generator.c
linking shared-object json/ext/generator.bundle

make "DESTDIR=" install
/opt/local/bin/ginstall -c -m 0755 generator.bundle ./.gem.20150619-33181-1sq30h/json/ext
make: /opt/local/bin/ginstall: No such file or directory
make: *** [install-so] Error 1

make install failed, exit code 2

Gem files will remain installed in /Users/gdanda/.rbenv/versions/2.0.0-p451/lib/ruby/gems/2.0.0/gems/json-1.8.3 for inspection.
Results logged to /Users/gdanda/.rbenv/versions/2.0.0-p451/lib/ruby/gems/2.0.0/extensions/x86_64-darwin-13/2.0.0-static/json-1.8.3/gem_make.out
</pre>

Now <strong>ginstall</strong> was preinstalled (maybe with coreutils) and needed to be just linked:

<pre>
sudo ln -s /usr/bin/install /opt/local/bin/ginstall
</pre>

And voila! I typed

<pre>bundle install</pre> 

and it worked like a charm after that.


