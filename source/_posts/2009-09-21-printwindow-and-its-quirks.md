---
title: PrintWindow and its quirks
author: Mart
excerpt: Taking a screen shot of another window with WinAPI can prove to be a big challenge. Partly because the lack of quality information and partly because of the bugs in Windows itself.
layout: post
permalink: /printwindow-and-its-quirks/
categories:
  - Guides
tags:
  - .NET
  - 'C#'
  - WinAPI
  - Windows
---
There are two ways to acquire a screen shot of an application window. One is by copying the device context of the window in question with <a href="http://msdn.microsoft.com/en-us/library/dd183370(VS.85).aspx" target="_blank">BitBlt</a>. The other is to let the window render itself into a device context specified by you.

## Copying the device context

The first option is the way to go if you are sure that the window you are getting the screen shot of is on top and not hidden by other windows. The reason being that if the window is not on top the windows that are on top of it will end up in your picture instead of the window you wanted to picture.

Here is a small example on how to use the first method in C#:  
(All of the P/Invokes used can be obtained from <a href="http://www.pinvoke.net/" target="_blank">pinvoke.net</a>)

<pre class="brush: csharp; title: ; notranslate" title="">IntPtr hWnd = ...;

Image screenshot = new Bitmap(..., ...);
using (Graphics ssg = Graphics.FromImage(screenshot))
{
    // Or use GetDC for only the client are of the window:
    IntPtr hDC = GetWindowDC(hWnd);
    BitBlt(
        ssg.GetHdc(), 0, 0, screenshot.Width, screenshot.Height,
        hDC, 0, 0,
        TernaryRasterOperations.SRCCOPY
    );

    ReleaseDC(hWnd, hDC);
    ssg.ReleaseHdc();
}

// Display the image in a PictureBox:
pictureBox1.Image = screenshot;
</pre>

## Rendering the window into a device context

<div id="attachment_137" class="wp-caption alignleft" style="width: 160px">
  <a href="http://www.roosmaa.net/wp-content/uploads/2009/09/PrintWindow_0.png"><img class="size-thumbnail wp-image-137 " title="Calculator by PrintWindow" src="http://www.roosmaa.net/wp-content/uploads/2009/09/PrintWindow_0-150x150.png" alt="Calculator by PrintWindow" width="150" height="150" /></a><p class="wp-caption-text">
    PrintWindow
  </p>
</div>

The second method utilizes the <a href="http://msdn.microsoft.com/en-us/library/dd162869(VS.85).aspx" target="_blank">PrintWindow</a> function which seems to use some black magic along with <a href="http://msdn.microsoft.com/en-us/library/dd145216(VS.85).aspx" target="_blank">WM_PRINT</a> message to obtain the picture of the window. In a nutshell it takes a device context and tells the window to render itself into it.

While it does get you the picture of the window even if it is under other windows it isn&#8217;t perfect. For instance it fails when the target window is minimized, so you need be sure that the window is in its restored state.

It occasionally has a bad habit of messing up the window of which it has taken the picture, guess it&#8217;s the black magic part in the function that no one knows about. For instance when restoring the window and using the PrintWindow function immediately after that, the widgets in the window thing they have rendered themselves, but in reality they haven&#8217;t. A way around it is to wait a bit after restoring the window.

<div id="attachment_136" class="wp-caption alignright" style="width: 160px">
  <a href="http://www.roosmaa.net/wp-content/uploads/2009/09/PrintWindow_1.png"><img class="size-thumbnail wp-image-136 " title="Calculator by PrintWindow (PW_CLIENTONLY)" src="http://www.roosmaa.net/wp-content/uploads/2009/09/PrintWindow_1-150x150.png" alt="Calculator by PrintWindow with PW_CLIENTONLY" width="150" height="150" /></a><p class="wp-caption-text">
    PrintWindow with PW_CLIENTONLY
  </p>
</div>

By default PrintWindow gets the picture of the whole window (including titlebar, menubar, borders, etc). In some cases this is not desired and from the PrintWindow <a href="http://msdn.microsoft.com/en-us/library/dd162869(VS.85).aspx" target="_blank">documentation</a> one can see that the PW_CLIENTONLY flag can be used for it.

Wrong! The PW_CLIENTONLY implementation is buggy, the rendered picture (on the right) is the size of the client area, but what&#8217;s on it is far from the client area. This can be worked around by using the default mode and removing the decorations manually.

The location of the client area to be cut out of the whole image can be calculated with the information from these three functions: <a href="http://msdn.microsoft.com/en-us/library/ms633503(VS.85).aspx" target="_blank">GetClientRect</a>, <a href="http://msdn.microsoft.com/en-us/library/ms633519(VS.85).aspx" target="_blank">GetWindowRect</a> and <a href="http://msdn.microsoft.com/en-us/library/aa931003.aspx" target="_blank">ClientToScreen</a>. Here is an example of how to do it:

<pre class="brush: csharp; title: ; notranslate" title="">IntPtr hWnd = ...;
Graphics ssg = ...;

// Calculate the offset of the clientArea
RECT clRect, wndRect;
POINT offset = new POINT { X = 0, Y = 0 };

GetClientRect(hWnd, out clRect);
GetWindowRect(hWnd, out wndRect);
ClientToScreen(hWnd, ref offset);

offset.X -= wndRect.Left;
offset.Y -= wndRect.Top;

// Get the whole window into a temporary buffer:
using (Image tmpImg = new Bitmap(wndRect.Width, wndRect.Height))
{
    using (Graphics tmp = Graphics.FromImage(tmpImg))
    {
        PrintWindow(hWnd, tmp.GetHdc(), 0x0);
        tmp.ReleaseHdc();
    }

    // Render only the client are to the output buffer:
    Rectangle clientArea = new Rectangle(offset.X, offset.Y, clRect.Width, clRect.Height);
    ssg.DrawImage(tmpImg, 0, 0, clientArea, GraphicsUnit.Pixel);
}
</pre>