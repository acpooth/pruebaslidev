# Slidev Example

This example contains 2 presentations. Can GitHub pages manage 2 presentations?

## Motivation

For SliDev, html, css, vue, etc starters, like me, is difficult to understand some subtleties that may be obvious to others more experienced.

For example, some thing so basic like, it is possible to host two or more slidev presentations in the same project page? The principal reason for this, is disk space. Each SliDev project (created with pnpm) uses a little less than 500 MB.

Well, it is possible to do so. In many ways:

  - Develop two presentations in the same folder.
  - Build both presentations as Single Page Applications (SPA).
  - Host both presentations using simple stand alone servers (python -m http.server)
  - Host both presentations in the same github repository
  
Here, I only write some painfully generated notes and hopefully improve it in the future.
  
## Notes

The following notes contains code and slidev commands using `pnpm`.

### Create a presentation project.

``` bash
pnpm create slidev
```

In the generated folder you can put more than one markdown files. Each of this file may contain an SliDev presentation. Here is the catch, the file slides.md is the default filename. However you can use any markdown filename!!!

### Presentation development.

If you run the comand `pnpm dev` in the project folder, you can visualize the `slides.md` presentation. At the moment you modify this file, the modifications are applied to the running html sever. So the trick here is specify the markdown file wich you want to work.

``` bash
# The alternative file in this repo is slides2.md
pnpm dev slides2.md
```

### Build the presentations as SPA and self hosting.

As stated, in slidev documentation. You can build to SPA many presentatiosn, for example:

``` bash
pnpm build slides.md slides2.md
```

In this way, the program will generate two folders inside the `dist` folder, one for each markdown file. However each folder is its own root/base. So make host them in an simple server is tricky.

Here is how you can host both files in the same server, using python as stand alone server. The important part is building the slides independently and specifying the --base option and output pathway.

``` bash
# Building PSA for first markdown file
pnpm build --out dist/slides --base /slides  slides.md

# Building PSA for second markdown file
pnpm build --out dist/slides2 --base /slides2  slides2.md

# move to the dist folder
cd dist

# run a python server
python http.server
```

And _voila_! you will have a simple web page with links to both presentation. If you click in one of the links it correctly redirects to the respective SPA.

It's __IMPORTANT__ the slash (/) at the begginning of the --base path.


### Now make it work in GitHub.

The important trick is the one shown in the previous section. Now, the way to apply this is following the SliDev documentation for GitHub hosting and modify the `.github/workflows/deploy.yml` file.

The specific part that you need to modify is the building part. The original file contains this.


``` yml
      - name: Build
        run: nr build --base /${{github.event.repository.name}}/
```


The modified file must contain one _build_ section for each markdown file. As the following example (from this repository).


``` yml
      - name: Build
        run: nr build --out ./dist/slides --base /${{github.event.repository.name}}/slides slides.md

      - name: Build
        run: nr build --out ./dist/slides2 --base /${{github.event.repository.name}}/slides2 slides2.md

```


Now you can, if the building ends without error, you can access both presentations using the modified url. For the example of this repo:

- __slides.md__ -> https://acpooth.github.io/pruebaslidev/slides
- __slides.md__ -> https://acpooth.github.io/pruebaslidev/slides2

Note that the las part of the url correspond to the --base path specified in the building.

That's all, :P

