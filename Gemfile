source "https://rubygems.org"

# GitHub Pages builds this site with Jekyll 3.10. Jekyll 3.8 required
# kramdown ~> 1.14, which cannot take the kramdown 2.3.x security fixes.
gem "jekyll", "~> 3.10.0"

# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.5"

# kramdown 2 ships GFM in a separate gem. _config.yml sets input: GFM.
# 2.3.1 is the first release that includes both kramdown security fixes.
gem "kramdown", ">= 2.3.1"
gem "kramdown-parser-gfm", "~> 1.1"

# Floors for advisories that Dependabot reports on transitive gems.
# These still satisfy Jekyll 3.10 (addressable ~> 2.4, webrick >= 1.0,
# and i18n's concurrent-ruby ~> 1.0).
gem "addressable", ">= 2.9.1"
gem "concurrent-ruby", ">= 1.3.7"
gem "webrick", ">= 1.8.2"

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.17"
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem "tzinfo-data", platforms: [:mingw, :mswin, :x64_mingw, :jruby]

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.0" if Gem.win_platform?
