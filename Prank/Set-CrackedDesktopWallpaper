function Set-CrackedDesktopWallpaper {
    param(
        [string]$CrackedImageUrl
    )
    # Load the System.Drawing assembly
    Add-Type -AssemblyName System.Drawing
    # Get the current wallpaper path
    $currentWallpaper = (Get-ItemProperty -Path 'HKCU:\Control Panel\Desktop\' -Name Wallpaper).Wallpaper
    # Extract the file extension from the current wallpaper
    $extension = [System.IO.Path]::GetExtension($currentWallpaper)
    # Define paths for the images and output using the same extension
    $wallpaperPath = "$env:USERPROFILE\Downloads\wallpaper_original$extension"
    $overlayPath   = "$env:USERPROFILE\Downloads\crackedscreenoverlay.png"
    $outputPath    = "$env:USERPROFILE\Downloads\yourcrackedwallpaper$extension"
    # Copy current wallpaper to the Downloads folder
    Copy-Item $currentWallpaper -Destination $wallpaperPath
    # Download the cracked screen overlay image
    Invoke-WebRequest -Uri $CrackedImageUrl -OutFile $overlayPath
    # Load the images
    $wallpaper = [System.Drawing.Image]::FromFile($wallpaperPath)
    $overlay   = [System.Drawing.Image]::FromFile($overlayPath)
    # Create a new bitmap with the same dimensions as the wallpaper
    $bitmap = New-Object System.Drawing.Bitmap $wallpaper.Width, $wallpaper.Height
    $graphics = [System.Drawing.Graphics]::FromImage($bitmap)
    # Draw the original wallpaper and the overlay
    $graphics.DrawImage($wallpaper, 0, 0, $wallpaper.Width, $wallpaper.Height)
    $graphics.DrawImage($overlay, 0, 0, $wallpaper.Width, $wallpaper.Height)
    # Save the combined image as a new file using the same extension
    $bitmap.Save($outputPath, [System.Drawing.Imaging.ImageFormat]::Png)  # This saves as PNG but maintains the original extension in the file name.
    # Clean up resources
    $graphics.Dispose()
    $wallpaper.Dispose()
    $overlay.Dispose()
    $bitmap.Dispose()
    # Set the new image as the desktop wallpaper using user32.dll
    $signature = @"
    using System;
    using System.Runtime.InteropServices;
    public class Wallpaper {
        [DllImport("user32.dll", CharSet = CharSet.Auto)]
        public static extern int SystemParametersInfo(int uAction, int uParam, string lpvParam, int fuWinIni);
    }
"@
    Add-Type $signature
    $SPI_SETDESKWALLPAPER = 20
    $SPIF_UPDATEINIFILE = 1
    $SPIF_SENDWININICHANGE = 2
    [Wallpaper]::SystemParametersInfo($SPI_SETDESKWALLPAPER, 0, $outputPath, $SPIF_UPDATEINIFILE -bor $SPIF_SENDWININICHANGE)
}