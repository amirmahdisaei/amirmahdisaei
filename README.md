DEV Community
Create account

33

75

Cover image for How to enable GitHub Actions on your Profile README for a snake-eating contribution graph 🐍
Michelle Mannering
Michelle Mannering
Posted on Jun 28, 2021 • Updated on Jun 5


81

11
How to enable GitHub Actions on your Profile README for a snake-eating contribution graph 🐍
#
github
#
markdown
#
opensource
#
tutorial
Git Good (10 Part Series)
1
Using the GitHub CLI to update your repo
2
How to create and pin a gist on GitHub
...
6 more parts...
9
How to create a Profile README for your Organisation
10
How to automatically close your issues once you merge a PR
Lots of people have been talking about and sharing their GitHub Profile READMEs. It was a project we launched last year and developers are loving it. As this feature became more popular, more developers were building templates, plugins, and apps for you to use on your profile.

This post from Supritha caught my attention. I like the way they talk about various pieces of code to have on your GitHub Profile README.

supritha 
How to have an awesome GitHub profile ?
Supritha ・ Jun 8 '21 ・ 5 min read
#github #markdown #opensource #git
One of the comments from Guillaume Falourd pointed to a really cool project from Platane.

GitHub logo Platane / snk
🟩⬜ Generates a snake game from a github user contributions graph and output a screen capture as animated svg or gif
snk
GitHub Workflow Status GitHub release GitHub marketplace type definitions code style

Generates a snake game from a github user contributions graph

github contribution grid snake animation
Pull a github user's contribution graph Make it a snake Game, generate a snake path where the cells get eaten in an orderly fashion.

Generate a gif or svg image.

Available as github action. It can automatically generate a new image each day. Which makes for great github profile readme

Usage
github action

- uses: Platane/snk@v2
  with
    # github user name to read the contribution graph from (**required**)
    # using action context var `github.repository_owner` or specified user
    github_user_name: ${{ github.repository_owner }}

    # list of files to generate.
    # one file per line. Each output can be customized with options as query string.
    #
    #  supported options:
    #  - palette:     A preset of color, one of [github, github-dark, github-light]
    #  - color_snake: Color of the snake
    #  - color_dots:  Coma separated list of dots color.
    #                 The
…
View on GitHub
It uses GitHub Actions to build and update a user's contribution graph, and then has a snake eat all your contributions. The output generates a gif file, that you can then show on your GitHub Profile README. I thought this was pretty cool, so I set about adding this to my profile.

Step 1. Setting up GitHub Actions
The first thing you want to ensure before you start this project, is that you have GitHub Actions setup. Head to your Profile and ensure the "Actions" tab is available. Everyone should now have this feature.

Actions Button

Next, head to Platane's Action (available on the Marketplace) and make a copy of the code. You'll need to add this snippet into your Actions file.

Step 2. Creating your GitHub Actions yaml file
This is definitely the part where I got stuck. When looking at the code, I wasn't sure exactly where to add the lines of code mentioned on Marketplace.

After looking at the way Platane had their Actions file setup, I was able to generate the code (with a little help from Bdougie of course).

I've added the full code snippet below and added plenty of comments to (hopefully make it easy to understand).

You can copy this code straight into a blank *.yml file. Make sure you create a new *.yml file under the following directory:

YOUR_GITHUB_USERNAME --> .github --> workflows --> FILE_NAME.yml

# GitHub Action for generating a contribution graph with a snake eating your contributions.

name: Generate Snake

# Controls when the action will run. This action runs every 6 hours.

on:
  schedule:
      # every 6 hours
    - cron: "0 */6 * * *"

# This command allows us to run the Action automatically from the Actions tab.
  workflow_dispatch:

# The sequence of runs in this workflow:
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:

    # Checks repo under $GITHUB_WORKSHOP, so your job can access it
      - uses: actions/checkout@v2

    # Generates the snake  
      - uses: Platane/snk@master
        id: snake-gif
        with:
          github_user_name: mishmanners
          # these next 2 lines generate the files on a branch called "output". This keeps the main branch from cluttering up.
          gif_out_path: dist/github-contribution-grid-snake.gif
          svg_out_path: dist/github-contribution-grid-snake.svg

     # show the status of the build. Makes it easier for debugging (if there's any issues).
      - run: git status

      # Push the changes
      - name: Push changes
        uses: ad-m/github-push-action@master
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: master
          force: true

      - uses: crazy-max/ghaction-github-pages@v2.1.3
        with:
          # the output branch we mentioned above
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}