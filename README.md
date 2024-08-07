OpenPGP.rb: OpenPGP for Ruby
============================

This is a pure-Ruby implementation of the OpenPGP Message Format (RFC 4880).

* <http://github.com/bendiken/openpgp>


### About This Fork
I implemented pure Ruby OpenPGP message encryption and decryption based on the original repository. The original repository only supported message parsing and building, and it had some bugs which I have fixed. This implementation is primarily for work purposes, specifically to communicate with a bank system. Therefore, only the necessary subset of features is implemented. But it should be sufficient for most use case. (encrypt, decrypt, sign with gzip-compress)

### About OpenPGP

OpenPGP is the most widely-used e-mail encryption standard in the world. It
is defined by the OpenPGP Working Group of the Internet Engineering Task
Force (IETF) Proposed Standard RFC 4880. The OpenPGP standard was originally
derived from PGP (Pretty Good Privacy), first created by Phil Zimmermann in
1991.

* <http://tools.ietf.org/html/rfc4880>
* <http://www.openpgp.org/>

Features
--------

* Encodes and decodes ASCII-armored OpenPGP messages.
* Parses OpenPGP messages into their constituent packets.
  * Supports both old-format (PGP 2.6.x) and new-format (RFC 4880) packets.
* Includes a GnuPG wrapper for features that are not natively supported.

Examples
--------

    require 'rubygems'
    require 'openpgp'

### Decoding an ASCII-armored message

    require 'open-uri'
    text = open('http://openpgp.rubyforge.org/pgp.txt').read

    msg = OpenPGP::Message.parse(OpenPGP.dearmor(text))
  
### Create a OpenPGP Message
```ruby=
    class OpenPGPRBAdapter < OpenPGPAdapter
      def initialize
        super()
        @kr = OpenPGP::KeyRing.new
      end

      def decrypt(armored_text, passphrase: nil)
        OpenPGP.collect_literal(@kr.decrypt(armored_text))
      end

      def encrypt(data, recipient:, signer: nil, signer_passphrase: nil, cipher_algo: :aes256, digest_algo: :sha256, compress_algo: :zip)
        msg = @kr.encrypt(data, recipient: recipient, signer: signer, cipher_algo: cipher_algo, digest_algo: digest_algo, compress_algo: compress_algo)
        msg.build
      end

      def detached_sign(data, signer:, passphrase: nil)
        msg = @kr.detached_sign(data, signer: signer)
        msg.build
      end

      def import(key_path, passphrase: nil)
        # support binary format
        content = File.read(key_path)
        content = OpenPGP::Message.parse(content) unless content.force_encoding("utf-8").valid_encoding?
        @kr.import(content, passphrase: passphrase)
      end
    end
```

### Generating a new keypair

    gpg = OpenPGP::Engine::GnuPG.new(:homedir => '~/.gnupg')
    key_id = gpg.gen_key({
      :key_type      => 'DSA',
      :key_length    => 1024,
      :subkey_type   => 'ELG-E',
      :subkey_length => 1024,
      :name          => 'J. Random Hacker',
      :comment       => nil,
      :email         => 'jhacker@example.org',
      :passphrase    => 'secret passphrase',
    })

Documentation
-------------

* <http://openpgp.rubyforge.org/>

Dependencies
------------

* [Ruby](http://ruby-lang.org/) (>= 1.8.7) or (>= 1.8.1 with [Backports][])
* [Open4](http://rubygems.org/gems/open4) (>= 1.0.1)

Installation
------------

The recommended installation method is via [RubyGems](http://rubygems.org/).
To install the latest official release of OpenPGP.rb, do:

    % [sudo] gem install openpgp             # Ruby 1.8.7+ or 1.9.x
    % [sudo] gem install backports openpgp   # Ruby 1.8.1+

Download
--------

To get a local working copy of the development repository, do:

    % git clone git://github.com/bendiken/openpgp.git

Alternatively, you can download the latest development version as a tarball
as follows:

    % wget http://github.com/bendiken/openpgp/tarball/master

Resources
---------

* <http://openpgp.rubyforge.org/>
* <http://github.com/bendiken/openpgp>
* <http://rubygems.org/gems/openpgp>
* <http://rubyforge.org/projects/openpgp/>
* <http://raa.ruby-lang.org/project/openpgp/>
* <http://www.ohloh.net/p/openpgp>

Authors
-------

* [Arto Bendiken](mailto:arto.bendiken@gmail.com) - <http://ar.to/>

Contributors
------------

* [Kévin Lacointe](mailto:kevinlacointe@gmail.com) - <http://github.com/klacointe>

Contributing
------------

* Do your best to adhere to the existing coding conventions and idioms.
* Don't use hard tabs, and don't leave trailing whitespace on any line.
* Do document every method you add using [YARD][] annotations. Read the
  [tutorial][YARD-GS] or just look at the existing code for examples.
* Don't touch the `.gemspec` or `VERSION` files. If you need to change them,
  do so on your private branch only.
* Do feel free to add yourself to the `CONTRIBUTORS` file and the
  corresponding list in the the `README`. Alphabetical order applies.
* Don't touch the `AUTHORS` file. If your contributions are significant
  enough, be assured we will eventually add you in there.
* Do note that in order for us to merge any non-trivial changes (as a rule
  of thumb, additions larger than about 15 lines of code), we need an
  explicit [public domain dedication][PDD] on record from you.

License
-------

OpenPGP.rb is free and unencumbered public domain software. For more
information, see <http://unlicense.org/> or the accompanying UNLICENSE file.

[YARD]:      http://yardoc.org/
[YARD-GS]:   http://rubydoc.info/docs/yard/file/docs/GettingStarted.md
[PDD]:       http://unlicense.org/#unlicensing-contributions
