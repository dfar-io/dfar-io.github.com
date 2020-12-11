---
title: Setting up nopCommerce 4.00+ For Use With VSCode
author: dfar

date: 2019-11-27T15:20:51+00:00
url: /setting-up-nopcommerce-4-00-for-use-with-vscode/
categories:
  - Technology

---
To get started with using nopCommerce and VSCode together, you&#8217;ll need to have the following:

  1. <a rel="noreferrer noopener" aria-label="VSCode  (opens in a new tab)" href="https://code.visualstudio.com/" target="_blank">VSCode</a> installed.
  2. The [C# extension][1] installed alongside VSCode.
  3. .NET Core 3.0 SDK
  4. If running nopCommerce 4.00, <a rel="noreferrer noopener" href="https://aka.ms/msbuild/developerpacks" target="_blank">.NET Framework v4.6.1 Developer Pack</a> 

## Quick Start

  1. <a rel="noreferrer noopener" aria-label="Download the source (opens in a new tab)" href="https://github.com/nopSolutions/nopCommerce/releases" target="_blank">Download the nopCommerce source</a> and copy the contents to a folder on your PC.
  2. Open the folder using VSCode.
  3. If you&#8217;re running nopCommerce 4.00, apply the changes from <a rel="noreferrer noopener" href="https://github.com/nopSolutions/nopCommerce/tree/c53a37d03558df9afc134dce5d8c6369b6db563e/src/Build" target="_blank">this commit</a>. 
  4. Run `dotnet restore` to get all dependencies required for running locally.
  5. In the directory `/Presentation/Nop.Web`, run `dotnet run`.
  6. Access http://localhost:55390 and run the installation process to get a database created.

## Reference

<https://www.jerriepelser.com/blog/using-vscode-for-csharp-development/>

<https://www.c-sharpcorner.com/article/getting-started-with-nopcommerce-using-visual-studio-code/>

 [1]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp