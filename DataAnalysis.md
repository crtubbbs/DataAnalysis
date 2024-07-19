---
title: "Spotify 2023 Analysis"
date: "July 18, 2024"
execute:
  keep-md: true
  df-print: paged
  warning: false
format:
  html:
    theme: cerulean
    code-fold: true
    code-line-numbers: true
editor_options: 
  chunk_output_type: inline
---



In this analysis, we aim to explore and visualize the top songs based on their streaming numbers across different platforms. We will particularly focus on identifying the top 10 songs released in 2023 and comparing them with the overall top 10 songs by streams. This analysis is crucial for understanding trends in music popularity and how recent releases stack up against all-time hits.

# Load Required Libraries

First, we load the necessary libraries. We use `readr` for reading the dataset, `dplyr` for data manipulation, `ggplot2` for visualization, and `plotly` for creating interactive plots.








We filter the dataset to select the top 10 songs released in the year 2023. This helps us focus on the most popular recent releases.

::: {.cell}

```{.r .cell-code}
top_10_songs_released_2023 <- data %>%
  filter(released_year == 2023) %>%
  arrange(desc(streams)) %>%
  head(10) %>%
  select(track_name, artist_name, streams)
top_10_songs_released_2023
```

::: {.cell-output-display}

`````{=html}
<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["track_name"],"name":[1],"type":["chr"],"align":["left"]},{"label":["artist_name"],"name":[2],"type":["chr"],"align":["left"]},{"label":["streams"],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"Flowers","2":"Miley Cyrus","3":"1316855716","_rn_":"1"},{"1":"Ella Baila Sola","2":"Eslabon Armado, Peso Pluma","3":"725980112","_rn_":"2"},{"1":"Shakira: Bzrp Music Sessions, Vol. 53","2":"Shakira, Bizarrap","3":"721975598","_rn_":"3"},{"1":"TQG","2":"Karol G, Shakira","3":"618990393","_rn_":"4"},{"1":"La Bebe - Remix","2":"Peso Pluma, Yng Lvcas","3":"553634067","_rn_":"5"},{"1":"Die For You - Remix","2":"Ariana Grande, The Weeknd","3":"518745108","_rn_":"6"},{"1":"un x100to","2":"Bad Bunny, Grupo Frontera","3":"505671438","_rn_":"7"},{"1":"Cupid - Twin Ver.","2":"Fifty Fifty","3":"496795686","_rn_":"8"},{"1":"PRC","2":"Natanael Cano, Peso Pluma","3":"436027885","_rn_":"9"},{"1":"OMG","2":"NewJeans","3":"430977451","_rn_":"10"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
`````

:::
:::



Here, we select the overall top 10 songs by their number of streams. This gives us an insight into the most popular songs regardless of their release year.

::: {.cell}

```{.r .cell-code}
top_10_songs_2023 <- data %>%
  arrange(desc(streams)) %>%
  head(10) %>%
  select(track_name, released_year, artist_name, streams)
top_10_songs_2023
```

::: {.cell-output-display}

`````{=html}
<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["track_name"],"name":[1],"type":["chr"],"align":["left"]},{"label":["released_year"],"name":[2],"type":["int"],"align":["right"]},{"label":["artist_name"],"name":[3],"type":["chr"],"align":["left"]},{"label":["streams"],"name":[4],"type":["dbl"],"align":["right"]}],"data":[{"1":"Blinding Lights","2":"2019","3":"The Weeknd","4":"3703895074","_rn_":"1"},{"1":"Shape of You","2":"2017","3":"Ed Sheeran","4":"3562543890","_rn_":"2"},{"1":"Someone You Loved","2":"2018","3":"Lewis Capaldi","4":"2887241814","_rn_":"3"},{"1":"Dance Monkey","2":"2019","3":"Tones and I","4":"2864791672","_rn_":"4"},{"1":"Sunflower - Spider-Man: Into the Spider-Verse","2":"2018","3":"Post Malone, Swae Lee","4":"2808096550","_rn_":"5"},{"1":"One Dance","2":"2016","3":"Drake, WizKid, Kyla","4":"2713922350","_rn_":"6"},{"1":"STAY (with Justin Bieber)","2":"2021","3":"Justin Bieber, The Kid Laroi","4":"2665343922","_rn_":"7"},{"1":"Believer","2":"2017","3":"Imagine Dragons","4":"2594040133","_rn_":"8"},{"1":"Closer","2":"2016","3":"The Chainsmokers, Halsey","4":"2591224264","_rn_":"9"},{"1":"Starboy","2":"2016","3":"The Weeknd, Daft Punk","4":"2565529693","_rn_":"10"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
`````

:::
:::


We combine the two datasets—top 10 songs released in 2023 and overall top 10 songs. This allows us to compare the two sets of songs in a single visualization. We use ggplot2 to create a base plot and then convert it to an interactive plot using plotly. The plot shows the top 10 songs by streams, distinguishing between songs released in 2023 and the overall top 10 songs. Hovering over a point displays the song name and artist name.

::: {.cell}

```{.r .cell-code}
top_10_songs_released_2023$type <- "Released in 2023"
top_10_songs_2023$type <- "Top 10 Overall"


combined_data <- bind_rows(top_10_songs_released_2023, top_10_songs_2023)

combined_data <- combined_data %>%
  group_by(type) %>%
  mutate(position = row_number())

p <- ggplot(combined_data, aes(x = position, y = streams/100000, group = type, color = type, linetype = type, text = paste("Track Name:", track_name, "<br>Artist:", artist_name))) +
  geom_line(size = 1.5) +
  geom_point(size = 3) +
  scale_x_continuous() +
  labs(title = "Top 10 Songs by Streams",
       x = "Track Position",
       y = "Number of Streams",
       color = "Category",
       linetype = "Category") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        legend.position = "bottom")


ggplotly(p, tooltip = "text")
```

::: {.cell-output-display}

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-e63bddbab13329948329" style="width:100%;height:464px;"></div>
<script type="application/json" data-for="htmlwidget-e63bddbab13329948329">{"x":{"data":[{"x":[1,2,3,4,5,6,7,8,9,10],"y":[13168.55716,7259.8011200000001,7219.7559799999999,6189.9039300000004,5536.3406699999996,5187.4510799999998,5056.7143800000003,4967.9568600000002,4360.2788499999997,4309.7745100000002],"text":["Track Name: Flowers <br>Artist: Miley Cyrus","Track Name: Ella Baila Sola <br>Artist: Eslabon Armado, Peso Pluma","Track Name: Shakira: Bzrp Music Sessions, Vol. 53 <br>Artist: Shakira, Bizarrap","Track Name: TQG <br>Artist: Karol G, Shakira","Track Name: La Bebe - Remix <br>Artist: Peso Pluma, Yng Lvcas","Track Name: Die For You - Remix <br>Artist: Ariana Grande, The Weeknd","Track Name: un x100to <br>Artist: Bad Bunny, Grupo Frontera","Track Name: Cupid - Twin Ver. <br>Artist: Fifty Fifty","Track Name: PRC <br>Artist: Natanael Cano, Peso Pluma","Track Name: OMG <br>Artist: NewJeans"],"type":"scatter","mode":"lines+markers","line":{"width":5.6692913385826778,"color":"rgba(248,118,109,1)","dash":"solid"},"hoveron":"points","name":"Released in 2023","legendgroup":"Released in 2023","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","marker":{"autocolorscale":false,"color":"rgba(248,118,109,1)","opacity":1,"size":11.338582677165356,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(248,118,109,1)"}},"frame":null},{"x":[1,2,3,4,5,6,7,8,9,10],"y":[37038.95074,35625.438900000001,28872.418140000002,28647.916720000001,28080.965499999998,27139.2235,26653.43922,25940.401330000001,25912.24264,25655.29693],"text":["Track Name: Blinding Lights <br>Artist: The Weeknd","Track Name: Shape of You <br>Artist: Ed Sheeran","Track Name: Someone You Loved <br>Artist: Lewis Capaldi","Track Name: Dance Monkey <br>Artist: Tones and I","Track Name: Sunflower - Spider-Man: Into the Spider-Verse <br>Artist: Post Malone, Swae Lee","Track Name: One Dance <br>Artist: Drake, WizKid, Kyla","Track Name: STAY (with Justin Bieber) <br>Artist: Justin Bieber, The Kid Laroi","Track Name: Believer <br>Artist: Imagine Dragons","Track Name: Closer <br>Artist: The Chainsmokers, Halsey","Track Name: Starboy <br>Artist: The Weeknd, Daft Punk"],"type":"scatter","mode":"lines+markers","line":{"width":5.6692913385826778,"color":"rgba(0,191,196,1)","dash":"dash"},"hoveron":"points","name":"Top 10 Overall","legendgroup":"Top 10 Overall","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","marker":{"autocolorscale":false,"color":"rgba(0,191,196,1)","opacity":1,"size":11.338582677165356,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(0,191,196,1)"}},"frame":null}],"layout":{"margin":{"t":43.762557077625573,"r":7.3059360730593621,"b":42.22428554020037,"l":54.794520547945211},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"title":{"text":"Top 10 Songs by Streams","font":{"color":"rgba(0,0,0,1)","family":"","size":17.534246575342465},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.55000000000000004,10.449999999999999],"tickmode":"array","ticktext":["2.5","5.0","7.5","10.0"],"tickvals":[2.5,5,7.5,10],"categoryorder":"array","categoryarray":["2.5","5.0","7.5","10.0"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-45,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"Track Position","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[2673.3156985000001,38675.409551500001],"tickmode":"array","ticktext":["10000","20000","30000"],"tickvals":[10000,20000,30000],"categoryorder":"array","categoryarray":["10000","20000","30000"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"Number of Streams","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"title":{"text":"Category","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"538026052acf":{"x":{},"y":{},"colour":{},"linetype":{},"text":{},"type":"scatter"},"538025734ef1":{"x":{},"y":{},"colour":{},"linetype":{},"text":{}}},"cur_data":"538026052acf","visdat":{"538026052acf":["function (y) ","x"],"538025734ef1":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

:::
:::

This line plot visualizes the streaming numbers of the top 10 songs in 2023 compared to the overall top 10 songs. The interactive plot allows for an in-depth comparison by showing detailed information on hover.










Analyze and Compare Top and Next 10 Songs
We then compare the top 10 songs with the next 10 songs in terms of streaming numbers and audio features. We use ggplot2 to create bar plots and box plots for visualization.



::: {.cell}

```{.r .cell-code}
top_10_songs <- data %>%
  arrange(desc(streams)) %>%
  head(10)

# Filter for next 10 songs
next_10_songs <- data %>%
  arrange(desc(streams)) %>%
  slice(11:20)

# Combine the two datasets
top_10_songs$type <- "Top 10"
next_10_songs$type <- "Next 10"

combined_data <- bind_rows(top_10_songs, next_10_songs)

# Plot streaming numbers
p_streams <- ggplot(combined_data, aes(x = reorder(track_name, -streams), y = streams/1000000, fill = type)) +
  geom_bar(stat = "identity", position = position_dodge()) +
  labs(title = "Streaming Numbers for Top 10 and Next 10 Songs",
       x = "Track Name",
       y = "Number of Streams (in millions)",
       fill = "Category") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

# Plot audio features
audio_features <- combined_data %>%
  select(track_name, type, danceability_ = danceability_., valence_ = valence_., energy_ = energy_., acousticness_ = acousticness_., instrumentalness_ = instrumentalness_., liveness_ = liveness_., speechiness_ = speechiness_.) %>%
  gather(feature, value, -track_name, -type)

p_features <- ggplot(audio_features, aes(x = feature, y = value, fill = type)) +
  geom_boxplot() +
  labs(title = "Audio Features Comparison for Top 10 and Next 10 Songs",
       x = "Audio Feature",
       y = "Value",
       fill = "Category") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

# Convert to interactive plots
ggplotly(p_streams)
```

::: {.cell-output-display}

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-f2a93533b718b6ae6457" style="width:100%;height:464px;"></div>
<script type="application/json" data-for="htmlwidget-f2a93533b718b6ae6457">{"x":{"data":[{"orientation":"v","width":[0.89999999999999858,0.89999999999999858,0.89999999999999858,0.89999999999999858,0.89999999999999858,0.89999999999999858,0.89999999999999858,0.89999999999999858,0.89999999999999858,0.89999999999999858],"base":[0,0,0,0,0,0,0,0,0,0],"x":[11,12,13,14,15,16,17,18,19,20],"y":[2559.529074,2557.975762,2513.1884930000001,2484.8129180000001,2420.4613380000001,2355.719893,2322.5801219999998,2303.0339730000001,2288.695111,2282.7714850000002],"text":["reorder(track_name, -streams): Perfect<br />streams/1e+06: 2559.529<br />type: Next 10","reorder(track_name, -streams): Heat Waves<br />streams/1e+06: 2557.976<br />type: Next 10","reorder(track_name, -streams): As It Was<br />streams/1e+06: 2513.188<br />type: Next 10","reorder(track_name, -streams): Se��o<br />streams/1e+06: 2484.813<br />type: Next 10","reorder(track_name, -streams): Say You Won't Let Go<br />streams/1e+06: 2420.461<br />type: Next 10","reorder(track_name, -streams): lovely - Bonus Track<br />streams/1e+06: 2355.720<br />type: Next 10","reorder(track_name, -streams): Watermelon Sugar<br />streams/1e+06: 2322.580<br />type: Next 10","reorder(track_name, -streams): Don't Start Now<br />streams/1e+06: 2303.034<br />type: Next 10","reorder(track_name, -streams): Lucid Dreams<br />streams/1e+06: 2288.695<br />type: Next 10","reorder(track_name, -streams): Sweater Weather<br />streams/1e+06: 2282.771<br />type: Next 10"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(248,118,109,1)","line":{"width":1.8897637795275593,"color":"transparent"}},"name":"Next 10","legendgroup":"Next 10","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[0.89999999999999991,0.90000000000000013,0.90000000000000036,0.90000000000000036,0.90000000000000036,0.90000000000000036,0.90000000000000036,0.89999999999999947,0.89999999999999858,0.89999999999999858],"base":[0,0,0,0,0,0,0,0,0,0],"x":[1,2,3,4,5,6,7,8,9,10],"y":[3703.895074,3562.5438899999999,2887.241814,2864.7916719999998,2808.0965500000002,2713.9223499999998,2665.343922,2594.040133,2591.2242639999999,2565.529693],"text":["reorder(track_name, -streams): Blinding Lights<br />streams/1e+06: 3703.895<br />type: Top 10","reorder(track_name, -streams): Shape of You<br />streams/1e+06: 3562.544<br />type: Top 10","reorder(track_name, -streams): Someone You Loved<br />streams/1e+06: 2887.242<br />type: Top 10","reorder(track_name, -streams): Dance Monkey<br />streams/1e+06: 2864.792<br />type: Top 10","reorder(track_name, -streams): Sunflower - Spider-Man: Into the Spider-Verse<br />streams/1e+06: 2808.097<br />type: Top 10","reorder(track_name, -streams): One Dance<br />streams/1e+06: 2713.922<br />type: Top 10","reorder(track_name, -streams): STAY (with Justin Bieber)<br />streams/1e+06: 2665.344<br />type: Top 10","reorder(track_name, -streams): Believer<br />streams/1e+06: 2594.040<br />type: Top 10","reorder(track_name, -streams): Closer<br />streams/1e+06: 2591.224<br />type: Top 10","reorder(track_name, -streams): Starboy<br />streams/1e+06: 2565.530<br />type: Top 10"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(0,191,196,1)","line":{"width":1.8897637795275593,"color":"transparent"}},"name":"Top 10","legendgroup":"Top 10","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.762557077625573,"r":7.3059360730593621,"b":166.79642372920054,"l":48.949771689497723},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"title":{"text":"Streaming Numbers for Top 10 and Next 10 Songs","font":{"color":"rgba(0,0,0,1)","family":"","size":17.534246575342465},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.40000000000000002,20.600000000000001],"tickmode":"array","ticktext":["Blinding Lights","Shape of You","Someone You Loved","Dance Monkey","Sunflower - Spider-Man: Into the Spider-Verse","One Dance","STAY (with Justin Bieber)","Believer","Closer","Starboy","Perfect","Heat Waves","As It Was","Se��o","Say You Won't Let Go","lovely - Bonus Track","Watermelon Sugar","Don't Start Now","Lucid Dreams","Sweater Weather"],"tickvals":[1,2,3,4.0000000000000009,5,6.0000000000000009,7,8,9,10,11,12.000000000000002,13,14.000000000000002,15,16,17,18,19,20],"categoryorder":"array","categoryarray":["Blinding Lights","Shape of You","Someone You Loved","Dance Monkey","Sunflower - Spider-Man: Into the Spider-Verse","One Dance","STAY (with Justin Bieber)","Believer","Closer","Starboy","Perfect","Heat Waves","As It Was","Se��o","Say You Won't Let Go","lovely - Bonus Track","Watermelon Sugar","Don't Start Now","Lucid Dreams","Sweater Weather"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-45,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"Track Name","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-185.19475370000001,3889.0898277000001],"tickmode":"array","ticktext":["0","1000","2000","3000"],"tickvals":[0,1000.0000000000001,2000,3000],"categoryorder":"array","categoryarray":["0","1000","2000","3000"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"Number of Streams (in millions)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"title":{"text":"Category","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"538014db49ee":{"x":{},"y":{},"fill":{},"type":"bar"}},"cur_data":"538014db49ee","visdat":{"538014db49ee":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

:::

```{.r .cell-code}
ggplotly(p_features)
```

::: {.cell-output-display}

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-1aafef7cf5f03a94c6ee" style="width:100%;height:464px;"></div>
<script type="application/json" data-for="htmlwidget-1aafef7cf5f03a94c6ee">{"x":{"data":[{"x":[2,2,2,2,2,2,2,2,2,2,7,7,7,7,7,7,7,7,7,7,3,3,3,3,3,3,3,3,3,3,1,1,1,1,1,1,1,1,1,1,4,4,4,4,4,4,4,4,4,4,5,5,5,5,5,5,5,5,5,5,6,6,6,6,6,6,6,6,6,6],"y":[60,76,52,76,40,35,55,79,44,61,17,53,66,77,45,12,56,68,22,41,45,53,73,52,56,30,82,79,48,81,16,44,34,4,69,93,12,1,38,5,0,0,0,0,0,0,0,0,0,2,11,9,31,8,9,10,34,10,33,10,2,9,6,3,5,3,5,8,24,3],"hoverinfo":"y","type":"box","fillcolor":"rgba(248,118,109,1)","marker":{"opacity":null,"outliercolor":"rgba(0,0,0,1)","line":{"width":1.8897637795275593,"color":"rgba(0,0,0,1)"},"size":5.6692913385826778},"line":{"color":"rgba(51,51,51,1)","width":1.8897637795275593},"name":"Next 10","legendgroup":"Next 10","showlegend":true,"xaxis":"x","yaxis":"y","frame":null},{"x":[2,2,2,2,2,2,2,2,2,2,7,7,7,7,7,7,7,7,7,7,3,3,3,3,3,3,3,3,3,3,1,1,1,1,1,1,1,1,1,1,4,4,4,4,4,4,4,4,4,4,5,5,5,5,5,5,5,5,5,5,6,6,6,6,6,6,6,6,6,6],"y":[50,83,50,82,76,77,59,77,75,68,38,93,45,54,91,36,48,74,64,49,80,65,41,59,50,63,76,78,52,59,0,58,75,69,54,1,4,4,41,16,0,0,0,0,0,0,0,0,0,0,9,9,11,18,7,36,10,23,11,13,7,8,3,10,5,5,5,11,3,28],"hoverinfo":"y","type":"box","fillcolor":"rgba(0,191,196,1)","marker":{"opacity":null,"outliercolor":"rgba(0,0,0,1)","line":{"width":1.8897637795275593,"color":"rgba(0,0,0,1)"},"size":5.6692913385826778},"line":{"color":"rgba(51,51,51,1)","width":1.8897637795275593},"name":"Top 10","legendgroup":"Top 10","showlegend":true,"xaxis":"x","yaxis":"y","frame":null}],"layout":{"margin":{"t":43.762557077625573,"r":7.3059360730593621,"b":81.049506567535644,"l":37.260273972602747},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"title":{"text":"Audio Features Comparison for Top 10 and Next 10 Songs","font":{"color":"rgba(0,0,0,1)","family":"","size":17.534246575342465},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.40000000000000002,7.5999999999999996],"tickmode":"array","ticktext":["acousticness_","danceability_","energy_","instrumentalness_","liveness_","speechiness_","valence_"],"tickvals":[1,2,3,4.0000000000000009,5,6,7],"categoryorder":"array","categoryarray":["acousticness_","danceability_","energy_","instrumentalness_","liveness_","speechiness_","valence_"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-45,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"Audio Feature","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-4.6500000000000004,97.650000000000006],"tickmode":"array","ticktext":["0","25","50","75"],"tickvals":[0,25,50.000000000000007,75],"categoryorder":"array","categoryarray":["0","25","50","75"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"Value","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"title":{"text":"Category","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"53802b8113e3":{"x":{},"y":{},"fill":{},"type":"box"}},"cur_data":"53802b8113e3","visdat":{"53802b8113e3":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

:::
:::

The bar plot displays the streaming numbers for the top 10 and next 10 songs, while the box plot compares key audio features between these two groups. Both plots are converted to interactive formats for better analysis.

Feedback: Roommates suggested that adding labels to the lines in the plot could help differentiate between categories more clearly. They also recommended adding explanations for why certain audio features might be relevant to streaming numbers and suggesting a comparison of audio feature distributions.
