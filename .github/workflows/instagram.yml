name: Dotekomanie Instagram Counter

on:
  workflow_dispatch:
  schedule:
    - cron: "0 * * * *"

jobs:
  update-counter:
    runs-on: ubuntu-latest

    steps:
      - name: Get Instagram follower count from Pulse
        id: pulse
        run: |
          RESPONSE=$(curl -sL "https://pulse.walls.sh/profile?url=https://www.instagram.com/dotekomanie")
          FOLLOWERS=$(echo "$RESPONSE" | jq -r '.followers')

          if [ -z "$FOLLOWERS" ] || [ "$FOLLOWERS" = "null" ]; then
            echo "Pulse did not return a follower count:"
            echo "$RESPONSE"
            exit 1
          fi

          echo "followers=$FOLLOWERS" >> "$GITHUB_OUTPUT"
          echo "Follower count: $FOLLOWERS"

      - name: Publish follower count to TC002 MQTT
        run: |
          sudo apt-get update
          sudo apt-get install -y mosquitto-clients

          mosquitto_pub \
            -h broker.emqx.io \
            -p 1883 \
            -t "dotekomanie-9p3K7xQ" \
            -m '{
              "text": [
                {
                  "content": "IG ${{ steps.pulse.outputs.followers }}",
                  "fontHeight": 10,
                  "color": "#FFFFFF",
                  "align": "left",
                  "valign": "top",
                  "rect": [0, 0, 52, 16],
                  "charSpacing": 1
                }
              ]
            }'
