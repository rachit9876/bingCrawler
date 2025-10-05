# Paste in COLAB

```
# First, install the required library
!pip install icrawler --quiet

import os
import time
from icrawler.builtin import BingImageCrawler

class NSFWBingCrawler(BingImageCrawler):
    def _request(self, url, **kwargs):
        if "adlt=" in url:
            url = url.replace("adlt=strict", "adlt=off")
        else:
            url += "&adlt=off"
        return super()._request(url, **kwargs)

def download_images(query, num_images, base="/content/downloaded_images"):
    """
    Download images from Bing with NSFW content allowed
    
    Args:
        query (str): Search term for images
        num_images (int): Number of images to download
        base (str): Base directory to save images
    
    Returns:
        str: Path where images were saved
    """
    # Create save directory
    save_dir = os.path.join(base, query.replace(" ", "_"))
    os.makedirs(save_dir, exist_ok=True)
    
    print(f"Downloading {num_images} images for query: '{query}'")
    print(f"Images will be saved to: {save_dir}")
    print("Starting download... (this may take a few minutes)")
    
    # Initialize and run crawler
    crawler = NSFWBingCrawler(storage={"root_dir": save_dir})
    crawler.crawl(keyword=query, max_num=num_images)
    
    return save_dir

def main():
    print("=" * 50)
    print("BING IMAGE DOWNLOADER (NSFW Enabled)")
    print("=" * 50)
    print()
    
    # Get user input
    search_term = input("Enter search term: ").strip()
    if not search_term:
        print("Error: Search term cannot be empty!")
        return
    
    try:
        num_files = int(input("Enter number of images to download: "))
        if num_files <= 0:
            print("Error: Number must be greater than 0!")
            return
        if num_files > 1000:
            print("Warning: Large number of images may take a long time!")
            confirm = input("Continue? (y/n): ").lower()
            if confirm != 'y':
                return
    except ValueError:
        print("Error: Please enter a valid number!")
        return
    
    print()
    
    # Download images
    try:
        path = download_images(search_term, num_files)
        print()
        print("✅ Download completed successfully!")
        print(f"📁 Images saved to: {path}")
        
        # Count downloaded files
        downloaded_count = len([f for f in os.listdir(path) if f.endswith(('.jpg', '.jpeg', '.png', '.gif'))])
        print(f"📊 Total images downloaded: {downloaded_count}")
        
    except Exception as e:
        print(f"❌ Error during download: {str(e)}")

if __name__ == "__main__":
    main()
```