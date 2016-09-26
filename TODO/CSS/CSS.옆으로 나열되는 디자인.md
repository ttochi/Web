# 옆으로 나열되는 디자인

```scss
#tag_info {
    float: left;
    width: 100%;
    background-color: $white-three;
    overflow-x: scroll;
    overflow-y: hidden;
    white-space: nowrap;

    > a {
      display: inline-block;
      margin: 10px 5px;
      width: 145px;
      height: 145px;

      > .img {
        position: relative;
        width: 100%;
        height: 100%;
        background-position: center;
        background-size: contain;

        > .selling {
          position: absolute;
          right: 0;
          bottom: 0;
          width: 44px;
          height: 44px;

          background: {
            image: url('/assets/web_action_icon.png');
            repeat: no-repeat;
            size: $web_action_icon_width;
            position: top -1512px left -279px;
            color: $bright-light-blue;
          }
        }
      }
    }
  }
```

밖에서 `white-space: no-warp` 해주고 안에서 `display: inline-block`