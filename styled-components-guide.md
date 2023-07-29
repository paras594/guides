
## Helpful Snippets



### Media query helper

```js
export const mediaSizes = {
  small: '576px',
  medium: '767px',
  large: '992px',
  extraLarge: '1200px',
  mobilePotrait: '480px',
};


export const device = {
  smallViewport: `@media only screen and (max-width: ${mediaSizes.small})`,
  mediumViewport: `@media only screen and (max-width: ${mediaSizes.medium})`,
  largeViewport: `@media only screen and (max-width: ${mediaSizes.large})`,
  extraLargeViewport: `@media only screen and (max-width: ${mediaSizes.extraLarge})`,
  mobilePotraitViewport: `@media only screen and (max-width: ${mediaSizes.mobilePotrait})`,
};

// usage
export const Header = styled.h2`
  font-weight: bold;
  font-size: 32px;
  line-height: 39px;

  ${device.largeViewport} {
    font-size: 24px;
    text-align: left;
    width: 100%;
  }
`;

```

