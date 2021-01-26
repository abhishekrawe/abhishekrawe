# ====================================================================================
# Inputs and configuration

inputs:

  # Personal user token
  # No additional scopes are needed unless you want to include private repositories metrics
  # Some plugins may also require additional scopes
  token:
    description: GitHub Personal Token
    required: true

  # Set to "${{ secrets.GITHUB_TOKEN }}"
  committer_token:
    description: GitHub Token used to commit metrics
    default: ""

  # Branch to commit
  committer_branch:
    description: The branch used to commit metrics
    default: ""

  # GitHub username
  # Optional, as it defaults "token"'s owner
  user:
    description: GitHub username
    default: ""

  # Output path for generated metrics, relative to repository's root
  filename:
    description: Path of SVG image output
    default: github-metrics.svg

  # Optimize SVG image with SVGO
  # It minifies and removes useless attributes
  # Some templates may not support this option
  optimize:
    description: SVG optimization
    default: yes

  # Setup additional templates from remote repositories (like forks)
  # Format is : user/repo@branch:template
  # To use a community template, set "template" option to "@template" (where template is the template name)
  setup_community_templates:
    description: Additional community templates to setup
    default: ""

  # Timezone used by metrics
  # See https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
  # Some plugins will use it to calibrate dates
  config_timezone:
    description: Timezone used
    default: ""

  # Metrics output type
  # Supported values are :
  # - svg
  # - png (does not support animations)
  # - jpeg (does not support animations and transparency)
  config_output:
    description: Output image type
    default: svg

  # Enable or disable SVG CSS animations
  config_animations:
    description: SVG CSS animations
    default: yes

  # Configure padding for output image (percentage)
  # It can be used to add padding to generated metrics if rendering is cropped or has too much empty space
  # You can specify one value (for both width and height) and two values (one for width and one for height, seperated by a comma)
  config_padding:
    description: Image padding
    default: 6%

  # Configure metrics content order (comma-separated values)
  # Specify in which order base and plugins will be displayed
  # It is not mandatory to specify all partials when using this option, in this case, remaining parts will be appended
  #
  # For example, to display "base.repositories" before "base.activity" and "base.community" in "classic template" you can use:
  #   config_order: base.header, base.repositories, base.activity+community
  #
  # See source/templates/*/partials/_.json for a list of supported partials for each template.
  config_order:
    description: Configure metrics content order
    default: ""

  # Number of repositories to use for metrics
  # A high number increase metrics accuracy, but will consume additional API requests when using plugins
  repositories:
    description: Number of repositories to use
    default: 100

  # Whether to include forked repositories into metrics
  repositories_forks:
    description: Include forks in metrics
    default: no

  # Template to use
  # See https://github.com/lowlighter/metrics/tree/master/source/templates for supported templates
  template:
    description: Template to use
    default: classic

  # Raw query parameters (JSON string)
  # Some templates may require additional parameters which you can specify here
  # Do not use this option to pass plugins parameters as they'll be overwritten by the other options
  query:
    description: Additional query parameters
    default: "{}"

  # Template base content
  # Pass a string of comma-separated values
  # To disable everything (like if you want to use a plugin as standalone), pass an empty string
  # Supported values are :
  # - "header" (name, commits calendar, ...)
  # - "activity" (commits, issues/pull requests opened, ...)
  # - "community" (following, stars, sponsors, ...)
  # - "repositories" (license, stars, forks, ...)
  # - "metadata" (svg generation metadata)
  base:
    description: Base content to include in metrics
    default: "header, activity, community, repositories, metadata"

  # Google PageSpeed plugin
  # Enable it to compute the performance of provided website
  plugin_pagespeed:
    description: Enable Google PageSpeed metrics for user's website
    default: no

  # Website to audit with PageSpeed
  # Leave empty to default to the website attached to "user"'s GitHub account
  plugin_pagespeed_url:
    description: Website to audit with PageSpeed
    default: ""

  # Display additional PageSpeed audit metrics
  # The following are displayed :
  # First Contentful Paint, Speed Index, Largest Contentful Paint, Time to Interactive, Total Blocking Time, Cumulative Layout Shift
  # See https://web.dev/performance-scoring/ and https://googlechrome.github.io/lighthouse/scorecalc/ for more informations
  plugin_pagespeed_detailed:
    description: Display additional PageSpeed metrics
    default: no

  # Display the final screenshot of audited website taken by PageSpeed audit
  plugin_pagespeed_screenshot:
    description: Display a screenshot of your website
    default: no

  # PageSpeed API token (optional, avoid hitting requests limit)
  # See https://developers.google.com/speed/docs/insights/v5/get-started for more informations
  plugin_pagespeed_token:
    description: Pagespeed personal token
    default: ""

  # Lines of code plugin
  # Compute added/removed line for each of you repositories from your contributors stats
  plugin_lines:
    description: Enable lines of code metrics
    default: no

  # Traffic plugin ("token" must have "repo" permission)
  # Count views on your repositories
  plugin_traffic:
    description: Enable repositories traffic metrics
    default: no

  # Coding habits plugin
  # Search in your recent activity what've recently did and deduce facts/charts
  plugin_habits:
    description: Enable coding habits metrics
    default: no

  # Number of activity events to base habits on
  # Capped to 1000
  plugin_habits_from:
    description: Number of activity events to use
    default: 200

  # Number of days to base habits on (older events will be discarded)
  # Capped to 30
  plugin_habits_days:
    description: Number of days to use
    default: 14

  # Display tidbits about your active hours/days, indent used (spaces/tabs), etc. deduced from recent activity
  plugin_habits_facts:
    description: Display habits facts based on recent activity
    default: yes

  # Display charts of most active time of the day and languages recently used
  plugin_habits_charts:
    description: Display recent coding activity charts
    default: no

  # Languages plugins
  # Compute the most used programming languages on your repositories
  plugin_languages:
    description: Enable most used languages metrics
    default: no

  # List of ignored languages, comma separated
  # Ignored languages won't count towards your languages metrics
  plugin_languages_ignored:
    description: List of ignored languages
    default: ""

  # List of skipped repositories, comma separated
  # Skipped repositories won't count towards your languages metrics
  plugin_languages_skipped:
    description: List of skipped repositories
    default: ""

  # List of custom colors for specified languages
  # Use this option when GitHub's color are too similar for your most used languages
  #
  # It is possible to use either hexadecimal colors or named colors
  # - Use "rainbow" values for a predefined set of colors
  # - Use `${n}:${color}` to change the color of the n-th most used language (e.g. "0:red" to make your most used language red)
  # - Use `${language}:${color}` to change the color of named language (e.g. "javascript:red" to make JavaScript language red, language case is ignored)
  plugin_languages_colors:
    description: Custom languages colors
    default: ""

  # Follow-up plugin
  # Display the number and the ratio of opened/closed issues and opened/merged pull requests on your repositories
  plugin_followup:
    description: Enable owned repositories issues and pull requests metrics
    default: no

  # Music plugin
  # Display tracks you recently listened or your favorite tracks from a playlist
  plugin_music:
    description: Enable music plugin
    default: no

  # Music provider
  # Required in "recent" mode
  # Optional in "playlist" mode (will be deduced from "plugin_music_playlist" url)
  # Supported values are :
  # - "apple" for Apple Music
  # - "spotify" for Spotify
  # - "lastfm" for Last.fm
  plugin_music_provider:
    description: Name of the music provider you're using
    default: ""

  # Music personal token
  # This may be required depending on the music provider and the mode you use
  # - "apple" : not required
  # - "spotify" : required for "recent" mode, format is "client_id, client_secret, refresh_token"
  # - "lastfm" : required, format is "api_key"
  plugin_music_token:
    description: Music provider personal token
    default: ""

  # Music plugin mode
  # Supported values are :
  # - "playlist" : display tracks from a playlist randomly
  # - "recent" : display recently played tracks
  plugin_music_mode:
    description: Use "recent" to display recently played music and "playlist" to display tracks randomly from a given playlist
    default: ""

  # Music playlist
  # The embed playlist url (source which is used for music player iframes)
  # Will default "plugin_music_mode" to "playlist" when set
  plugin_music_playlist:
    description: Embed playlist url
    default: ""

  # Number of tracks to display for music plugin
  plugin_music_limit:
    description: Number of tracks to display
    default: 4

  # Music service username
  # Leave empty to default to the login "user"'s GitHub account
  plugin_music_user:
    description: "Music provider username"
    default: ""

  # Posts plugin
  # Display recent posts from an external source
  plugin_posts:
    description: Enable recent posts display
    default: no

  # Posts source
  # This is required when "plugin_posts" is enabled
  # Supported values are :
  # - "dev.to" for dev.to
  plugin_posts_source:
    description: Posts external source
    default: ""

  # Posts source username
  # Leave empty to default to the login "user"'s GitHub account
  plugin_posts_user:
    description: Posts external source username
    default: ""

  # Number of posts to display
  plugin_posts_limit:
    description: Number of posts to display
    default: 4

  # Isometric calendar plugin
  # Display an isometric view of your commits calendar along with a few stats like current streak and average commits per day
  plugin_isocalendar:
    description: Display an isometric view of your commits calendar along with a few additional stats
    default: no

  # Duration shown by isometric calendar plugin
  # Supported values are "half-year" and "full-year"
  plugin_isocalendar_duration:
    description: Set isometric calendar duration
    default: half-year

  # Gists plugin
  # Display gists metrics
  plugin_gists:
    description: Display gists metrics
    default: no

  # Topics plugin
  # Display starred topics
  plugin_topics:
    description: Display starred topics
    default: no

  # Topics plugin mode
  # Change the way topics are displayed
  # Supported values are :
  # - "starred" to display starred topics as interests labels
  # - "mastered" to display starred topics as mastered/known technologies icons
  plugin_topics_mode:
    description: Display starred topics
    default: "starred"

  # Sorting method of displayed topics
  # Supported values are :
  # - "stars" to sort them from most starred to least starred
  # - "activity" to sort them from most recent activity to least recent activity
  # - "starred" to sort them from your most recently starred to your least recently starred
  # - "random" to sort them randomly
  plugin_topics_sort:
    description: Sorting method of starred topics
    default: "stars"

  # Number of topics to display (between 0 and 20)
  # If more topics must be displayed, they will be grouped in an ellipsis
  plugin_topics_limit:
    description: Number of starred topics to display
    default: ""

  # Projects plugin
  # Display active projects
  plugin_projects:
    description: Display active projects
    default: no

  # Display active repository projects
  # By default, only user owned projects are displayed, with this option it is possible to display projects from repositories
  # List of comma-separated projects identifier, in the following format :user/:repo/projects/:project_id
  # The limit of displayed projects will automatically be updated so listed projects will fit
  plugin_projects_repositories:
    description: List of repository project identifiers to disaplay
    default: ""

  # Number of active projects to display
  # Between 1 and 100
  plugin_projects_limit:
    description: Number of active projects to display
    default: 4

  # Display projects descriptions if enabled
  plugin_projects_descriptions:
    description: Display projects descriptions
    default: no

  # Tweets plugin
  # Enable it to display recent tweets of the twitter username attached to "user"
  plugin_tweets:
    description: Display recent tweets
    default: no

  # Twitter username
  # Leave empty to default to the twitter account attached to "user"'s GitHub account
  plugin_tweets_user:
    description: Twitter username
    default: ""

  # Tweets API token (required when tweets plugin is enabled)
  # See https://apps.twitter.com for more informations
  plugin_tweets_token:
    description: Twitter bearer token
    default: ""

  # Number of tweets to display
  # Between 1 and 10
  plugin_tweets_limit:
    description: Number of tweets to display
    default: 2

  # Display recently starred repositories
  plugin_stars:
    description: Display recently starred repositories
    default: no

  # Number of recently starred repositories to display
  plugin_stars_limit:
    description: Number of recently starred repositories to display
    default: 4

  # Display stargazers evolution over the last two weeks
  # It shows total stargazers along with increase rate per day
  plugin_stargazers:
    description: Display stargazers evolution over the last two weeks
    default: no

  # Display recent activity
  plugin_activity:
    description: Display recent activity
    default: no

  # Number of activity events to display
  # Capped to 100
  plugin_activity_limit:
    description: Number of activity events to display
    default: 5

  # Discard older events
  # Use 0 to display activity whatever the date
  plugin_activity_days:
    description: Maximum activity event age
    default: 14

  # Events type to display
  # Pass a string of comma-separated values
  # Supported values are
  # - "comment" for all kind of comments (commits, issue and pr)
  # - "ref/create" and "ref/delete" for tag and branch creation/deletion
  # - "release" for new published releases
  # - "wiki" for wiki edition
  # - "push" for pushed commits
  # - "issue" and "pr" for issues and pull requests
  # - "review" for pull requests review
  # - "public" for repositories made public
  # - "fork" and "star" for forked and starred repositories
  # - "member" for accepted repository invitations
  plugin_activity_filter:
    description: Events to display
    default: all

  # Display followed and following users
  plugin_people:
    description: Display
    default: no

  # Limit the number of users displayed
  plugin_people_limit:
    description: Number of users to display per categorie
    default: 28

  # Configure image size of users' avatar
  plugin_people_size:
    description: Size of users' avatars
    default: 28

  # List of users categories to display (comma separated)
  # For user's metrics, supported values are:
  # - "followers"
  # - "following"/"followed"
  # - "sponsors"/"sponsorshipsAsMaintainer"
  # - "sponsoring"/"sponsored"/"sponsorshipsAsSponsor"
  # - "thanks" (see "plugin_people_thanks" below)
  # For repositories' metrics, supported values are:
  # - "contributors"
  # - "stargazers"
  # - "watchers"
  # - "sponsors"/"sponsorshipsAsMaintainer"
  # - "thanks" (see "plugin_people_thanks" below)
  plugin_people_types:
    description: Categories to display
    default: followers, following

  # List of users to thanks (comma seperated)
  # When using "thanks" as a type, it'll display the users you listed in this option
  # This can be used to create "Special thanks" badges that you can embed elsewhere
  plugin_people_thanks:
    description: Users to thanks in "thanks" section type
    default: ""

  # Display GitHub identicons instead of users' real avatar
  # Mostly for privacy purposes
  plugin_people_identicons:
    description: Use identicons instead of real avatars
    default: no

  # Display your favorites animes and mangas from AniList
  plugin_anilist:
    description: Display your favorites animes and mangas from AniList
    default: no

  # Medias to display from AniList (comma-separated list)
  # Supported values are:
  # - "anime"
  # - "manga"
  plugin_anilist_medias:
    description: Medias to display from AniList data
    default: anime, manga

  # Sections to display from AniList data (comma-separated list)
  # Values in "plugin_anilist_medias" may also impact displayed sections
  # Supported values are:
  # - "favorites" for favorites animes/mangas
  # - "watching" for currently watched animes
  # - "reading" for currently read mangas
  # - "characters" for favorites characters
  plugin_anilist_sections:
    description: Sections to display from AniList data
    default: favorites

  # Maximum number of medias to display per section from AniList Data
  plugin_anilist_limit:
    description: Medias to display
    default: 2

  # Shuffle AniList data
  plugin_anilist_shuffle:
    description: Shuffle AniList data
    default: yes

  # Username on AniList
  # Default to GitHub username
  plugin_anilist_user:
    description: AniList login
    default: ""

  # ====================================================================================
  # Options below are mostly used for testing

  # When enabled, any plugins errors will throw
  # By default, metrics are still generated with an error message
  plugins_errors_fatal:
    description: Die on plugins errors
    default: no

  # Enable debug mode
  debug:
    description: Enable debug logs
    default: no

  # Verify SVG after generation
  # Test whether SVG can be correctly parsed
  verify:
    description: Verify SVG after generation
    default: no

  # Debug flags
  debug_flags:
    description: Debug flags
    default: ""

  # Enable dry-run mode
  # Generate image but does not push it
  dryrun:
    description: Enable dry-run
    default: no

  # Use mocked data
  # Bypass external APIs which requires a token and sent mocked data
  use_mocked_data:
    description: Use mocked data instead of real APIs
    default: no

  # Use pre-built image from GitHub registry (experimental)
  # See https://github.com/users/lowlighter/packages/container/package/metrics for more information
  use_prebuilt_image:
    description: Use pre-built image from GitHub registry
    default: ""

# ====================================================================================
# Action metadata
name: GitHub metrics as SVG image
author: lowlighter
description: An SVG generator with 20+ metrics about your GitHub account! Additional plugins are available to display even more!
branding:
  icon: user-check
  color: gray-dark

# The action will parse its name to check if it's the official action or if it's a forked one
# On the official action, it'll use the docker image published on GitHub registry when using a released version, allowing faster runs
# On a forked action, it'll rebuild the docker image from Dockerfile to take into account changes you made
runs:
  using: composite
  steps:
    - run: |
        # Create environment file from inputs and GitHub variables
        cd $METRICS_ACTION_PATH
        touch .env
        for INPUT in $(echo $INPUTS | jq -r 'to_entries|map("INPUT_\(.key|ascii_upcase)=\(.value|@uri)")|.[]'); do
          echo $INPUT >> .env
        done
        env | grep -E '^(GITHUB|ACTIONS|CI)' >> .env
        echo "Environment variable: loaded"
        # Source repository (picked from action name)
        METRICS_SOURCE=$(echo $METRICS_ACTION | sed -E 's/metrics.*?$//g')
        echo "Source: $METRICS_SOURCE"
        # Version (picked from package.json)
        METRICS_VERSION=$(grep -Po '(?<="version": ").*(?=")' package.json)
        echo "Version: $METRICS_VERSION"
        # Image tag (extracted from version or from env)
        METRICS_TAG=v$(echo $METRICS_VERSION | sed -r 's/^([0-9]+[.][0-9]+).*/\1/')
        if [[ $METRICS_USE_PREBUILT_IMAGE ]]; then
          METRICS_TAG=$METRICS_USE_PREBUILT_IMAGE
          echo "Pre-built image: yes"
        fi
        echo "Image tag: $METRICS_TAG"
        # Image name
        # Pre-built image
        if [[ $METRICS_USE_PREBUILT_IMAGE ]]; then
          echo "Using pre-built version $METRICS_TAG, will pull docker image from GitHub registry"
          METRICS_IMAGE=ghcr.io/lowlighter/metrics:$METRICS_TAG
        # Official action
        elif [[ $METRICS_SOURCE == "lowlighter" ]]; then
          # Is unreleased version
          set +e
          METRICS_IS_RELEASED=$(expr $(expr match $METRICS_VERSION .*-beta) == 0)
          set -e
          echo "Is released version: $METRICS_IS_RELEASED"
          # Rebuild image for unreleased version
          if [[ "$METRICS_IS_RELEASED" -gt "0" ]]; then
            echo "Using released version $METRICS_TAG, will pull docker image from GitHub registry"
            METRICS_IMAGE=ghcr.io/lowlighter/metrics:$METRICS_TAG
          # Use registry for released version
          else
            echo "Using an unreleased version, rebuilding docker image from Dockerfile"
            docker build -t metrics:unreleased .
            METRICS_IMAGE=metrics:unreleased
          fi
        # Forked action
        else
          echo "Using a forked version, rebuilding docker image from Dockerfile"
          docker build -t metrics:forked .
          METRICS_IMAGE=metrics:forked
        fi
        echo "Image name: $METRICS_IMAGE"
        # Run docker image with current environment
        docker run --volume $GITHUB_EVENT_PATH:$GITHUB_EVENT_PATH --env-file .env $METRICS_IMAGE
        rm .env
      shell: bash
      env:
        METRICS_ACTION: ${{ github.action }}
        METRICS_ACTION_PATH: ${{ github.action_path }}
        METRICS_USE_PREBUILT_IMAGE: ${{ inputs.use_prebuilt_image }}
        INPUTS: ${{ toJson(inputs) }}



### Hi there, I' m ABHISHEK RAWE  👋

## ||Passionate developer || Programmer || CSE || Full stack||
- 🌱 I’m currently learning everything 🤣
- 💖Who loves to develop and design innovative and interactive websites.
- 🐱‍🚀I am a tech freak and always eager to expand my skills.
- 🥅 2021 Goals: Contribute more to Open Source projects.


### Spotify Playing 🎧

[<img src="https://now-playing-codestackr.vercel.app/api/spotify-playing" alt="codeSTACKr Spotify Playing" width="350" />](https://open.spotify.com/user/swyqyimdc12jajde4vpwd2x1b)


### Connect with me:


[<img align="left" alt="abhisahekrawe" width="22px" src="https://raw.githubusercontent.com/iconic/open-iconic/master/svg/globe.svg" />][website]
[<img align="left" alt="codeSTACKr | YouTube" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/youtube.svg" />][youtube]
[<img align="left" alt="codeSTACKr | Twitter" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/twitter.svg" />][twitter]
[<img align="left" alt="codeSTACKr | LinkedIn" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/linkedin.svg" />][linkedin]
[<img align="left" alt="codeSTACKr | Instagram" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/instagram.svg" />][instagram]
[<img align="left" alt="codeSTACKr | Instagram" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/medium.svg" />][instagram]


<br />


### Languages and Tools:

<img align="left" alt="Visual Studio Code" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/visual-studio-code/visual-studio-code.png" />
<img align="left" alt="HTML5" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/html/html.png" />
<img align="left" alt="CSS3" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/css/css.png" />
<img align="left" alt="Sass" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/sass/sass.png" />
<img align="left" alt="JavaScript" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/javascript/javascript.png" />
<img align="left" alt="React" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/react/react.png" />
<img align="left" alt="Node.js" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/nodejs/nodejs.png" />
<img align="left" alt="SQL" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/sql/sql.png" />
<img align="left" alt="MySQL" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/mysql/mysql.png" />
<img align="left" alt="GitHub" width="26px" src="https://raw.githubusercontent.com/github/explore/78df643247d429f6cc873026c0622819ad797942/topics/github/github.png" />
<img align="left" alt="Terminal" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/terminal/terminal.png" />
<img align="left" alt="MongoDB" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/mongodb/mongodb.png" />
<img align="left" alt="Git" width="26px" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/git/git.png" />
</br>

---

### 📕 Latest Blog Posts

<!-- BLOG-POST-LIST:START -->
- [Best Channel for learning Javascript](https://medium.com/@Abhishek_rawe/10-best-youtube-channels-for-learning-javascript-595b3d0b2df5)
- [Frontend, Backend, FullStack Developer](https://medium.com/@Abhishek_rawe/what-is-a-front-end-back-end-full-stack-developer-122ea0a8f0b0)
- [Top Tools for Making website Without Coding](https://medium.com/@Abhishek_rawe/tools-to-make-websites-without-coding-skills-e94990497f03)
- [Website for Learning Coding for Free](https://medium.com/@Abhishek_rawe/website-to-learn-coding-for-free-845926961337)
- [Top Coding Platform for Coding and Challanges ](https://medium.com/@Abhishek_rawe/top-5-popular-website-for-coding-challenges-and-for-practices-9d32e06f691b)
<!-- BLOG-POST-LIST:END -->

➡️ [more blog posts...](https://medium.com/@Abhishek_rawe)

---

[website]: https://abhishekrawe.github.io/abportfolio/
[medium]: https://medium.com/@Abhishek_rawe
[twitter]: https://twitter.com/codeSTACKr
[youtube]: https://youtube.com/abhishekrawe
[instagram]: https://instagram.com/abhishek_rawe
[linkedin]: https://www.linkedin.com/in/abhishek-rawe-11b4011aa
[webdevplaylist]: https://www.youtube.com/playlist?list=PLkwxH9e_vrAJ0WbEsFA9W3I1W-g_BTsbt
[jsplaylist]: https://www.youtube.com/playlist?list=PLkwxH9e_vrALRJKu7wfXby3MKeflhTu6B
[cssplaylist]: https://www.youtube.com/playlist?list=PLkwxH9e_vrALSdvZuEh6gqQdmDoDIoqz4
[reactplaylist]: https://www.youtube.com/playlist?list=PLkwxH9e_vrAK4TdffpxKY3QGyHCpxFcQ0



