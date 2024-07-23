## PLayo Nuxt YouTube Music Player

This is a simple music player application built using Nuxt.js and the YouTube IFrame Player API. It allows you to search for music videos on YouTube, queue them up, and play them back seamlessly without ads.

### Features

*   **Search for Music:** Find your favorite music videos on YouTube using the integrated search functionality.
*   **Create and Manage Queue:** Add videos to the queue and manage the playback order.
*   **Ad-Free Playback:** Enjoy uninterrupted listening with no ads.
*   **Theme Selection:** Customize the look and feel of the player with multiple theme options.
*   **Loop Queue:**  Automatically replay the queue from the beginning when it ends.

### Project Setup

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/2105789/playo.git
    ```

2.  **Navigate to the project directory:**

    ```bash
    cd nuxt-youtube-music-player
    ```

3.  **Install dependencies:**

    ```bash
    npm install
    ```

4.  **Get your YouTube API Key**

    *   Create a project on the [Google Cloud Console](https://console.cloud.google.com/).
    *   Enable the YouTube Data API v3.
    *   Create an API Key and restrict it for security. 
    *   Replace the placeholder `awiks` in your project components (`/components/home.vue`, `/components/search.vue`) with your actual YouTube API key.

5.  **Run the development server:**

    ```bash
    npm run dev
    ```

### File Structure

```
nuxt-youtube-music-player/
│   
├── components/
│   ├── home.vue          // Component displaying trending music
│   ├── search.vue         // Component for searching music videos
│   ├── queue.vue          // Component for managing the playback queue
│   └── player.vue        // Component handling the YouTube player
│   
└── pages/
    └── index.vue           // Main application page layout 
```

### Usage

1.  **Search:** Use the search bar to find your desired music videos.
2.  **Add to Queue:** Click the "Add to Queue" button on search results or the trending music section to add videos to the playback queue.
3.  **Manage Queue:** The "Queue" tab allows you to view your queued videos. You can reorder or remove them as needed.
4.  **Playback Controls:** Control playback with the play/pause button, volume slider, and progress bar.
5.  **Theme Selection:** Click the settings icon to choose from various themes to personalize your music player.

### Future Improvements

*   **User Authentication:** Implement user accounts to save playlists and preferences.
*   **Improved Queue Management:** Add features such as shuffle and repeat for more control over playback.
*   **Enhanced Search Functionality:**  Allow users to refine searches by artist, album, or genre.
*   **Offline Playback:** Explore options for caching songs for offline listening. 
*   **Mobile Responsiveness:** Optimize the user interface for seamless mobile usage.

## License

This project is open source. Feel free to modify and distribute it as you see fit.
