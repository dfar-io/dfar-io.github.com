---
title: Developing with nopCommerce using VSCode and Linux
author: dfar

date: 2020-04-21T15:59:00+00:00
url: /developing-with-nopcommerce-using-vscode-and-linux/
categories:
  - Technology

---
## Pre-requisites

To get started, you&#8217;ll need to set up:

  * VSCode with the C# and vscode-solution-explorer extensions installed
  * [SQL Server][1]
  * dotnet SDK
  * The library `libgdiplus` to run RoxyFileman.

## Download and Build nopCommerce Source Code

To get started, get a copy of the nopCommerce source code at <a rel="noreferrer noopener" href="https://github.com/nopSolutions/nopCommerce/releases" target="_blank">their Github page</a>.

After downloading the source code, open the /src folder using VSCode.

For cleaning and building the project, you have two choices:

  1. Use `dotnet` to run `clean` and `build` while in the `src/` directory.
  2. Using the solution viewer plugin, you can clean and build the project:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/04/image-622x1024.png" alt="" class="wp-image-1156" srcset="https://40.76.37.251/wp-content/uploads/2020/04/image-622x1024.png 622w, https://40.76.37.251/wp-content/uploads/2020/04/image-182x300.png 182w, https://40.76.37.251/wp-content/uploads/2020/04/image-768x1264.png 768w, https://40.76.37.251/wp-content/uploads/2020/04/image.png 860w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /> </figure> 

After cleaning and building, you can run using either:

  1. `dotnet run` in the `src/Presentation/Nop.Web` directory.
  2. Using the VSCode solution explorer.

Once started running, you&#8217;ll be able to access nopCommerce below:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/04/image-1-1024x141.png" alt="" class="wp-image-1157" srcset="https://40.76.37.251/wp-content/uploads/2020/04/image-1-1024x141.png 1024w, https://40.76.37.251/wp-content/uploads/2020/04/image-1-300x41.png 300w, https://40.76.37.251/wp-content/uploads/2020/04/image-1-768x106.png 768w, https://40.76.37.251/wp-content/uploads/2020/04/image-1-1200x165.png 1200w, https://40.76.37.251/wp-content/uploads/2020/04/image-1.png 1256w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

With this, you are able to run locally and perform all tasks related to administering nopCommerce, including running the installation and anything else.

 [1]: https://dfar.io/setting-up-sql-server-in-arch-linux/