# Inqud Website: Sample Next js project

## Description

This project is a business website developed using modern technologies to ensure an efficient and user-friendly experience. With this website, your business can showcase its products, services, and company information to a wide range of clients.

## Technology Stack

- @mui/material: React component library that allows for quick and convenient development of stylish user interfaces.
- next-intl: Next.js plugin providing internationalization support for your website.
- next: React framework for building high-performance web applications.
- react-datocms: Library for interacting with DatoCMS, the leading headless CMS (Content Management System).
- react-hook-form: Library for working with forms in React using hooks.

## Examples of works

- [Integration Datocms](#integration-datocms) 
- [Integration Localization (Datocms with next-intl)](#integration-localization-(datocms-with-next-intl))
- [Anchor links with sidebar](#anchor-links-with-sidebar)


## #Integration Datocms

DatoCMS is a web-based content management system (CMS) that focuses on providing convenient tools for creating and managing content for websites and web applications. It prioritizes developers by offering a powerful API and integration tools while also providing a user-friendly interface for content editors. With flexible content types, templates, and fields, DatoCMS allows for customization to suit various project needs. Additionally, its emphasis on speed and productivity ensures efficient content creation, editing, and publishing processes.

### Step 1:

Create optimizing calls to DatoCMS:

```
export async function performRequest({
  query,
  variables = {},
  includeDrafts = false,
  excludeInvalid = false,
  visualEditingBaseUrl,
  revalidate = 360,
}) {
  const { data } = await dedupedFetch(
    JSON.stringify({
      method: 'POST',
      headers: {
        Authorization: `Bearer ${process.env.NEXT_PUBLIC_DATOCMS_API_TOKEN}`,
        ...(includeDrafts ? { 'X-Include-Drafts': 'true' } : {}),
        ...(excludeInvalid ? { 'X-Exclude-Invalid': 'true' } : {}),
        ...(visualEditingBaseUrl
          ? {
              'X-Visual-Editing': 'vercel-v1',
              'X-Base-Editing-Url': visualEditingBaseUrl,
            }
          : {}),
        ...(process.env.NEXT_DATOCMS_ENVIRONMENT
          ? { 'X-Environment': process.env.NEXT_DATOCMS_ENVIRONMENT }
          : {}),
      },
      body: JSON.stringify({ query, variables, revalidate }),
      next: { revalidate },
    })
  )
  return data
}
```

We'll then use this function in all of our components that need to fetch content from DatoCMS.

### Step 2

Create model and set content in Datocms

![Datocms model](image.png)

### Step 3

Add content to modal

![Content modal](image-1.png)

### Step 4 

Create query

```
export const HOME_PAGE_QUERY = `
query MyQuery($locale: SiteLocale) {
  homePage(locale: $locale) {
    buttonScreen1ContactSales
    buttonScreen1GetStarted
    description {
      value
    }
    features
    id
    subTitle
    title
    buttonScreen2
  }
}
`
```

### Step 5

Get Data

```
export default async function HeroSection({ params }) {
  const { homePage: data } = await getData(HOME_PAGE_QUERY, {
    locale: params.locale,
  })

  return (
    <section className={styles.wrapper}>
      <div className={clsx(styles.container, 'container')}>
        <div className={styles.leftSide}>
          <AnimatedMobile />

          <StyledTypographyIBMH5 className={styles.subTitle}>
            {data.subTitle}
          </StyledTypographyIBMH5>
          <StyledTypographyUrbanistH1 className={styles.title}>
            {data.title}
          </StyledTypographyUrbanistH1>
          <StyledTypographyUrbanistH5
            component='div'
            className={styles.paragraph}
          >
            <StructuredText data={data.description} />
          </StyledTypographyUrbanistH5>

          <ul className={styles.features}>
            {data.features.map((text) => (
              <li key={text}>
                <Image src={Check} alt='check' />
                <StyledTypographyUrbanistBody
                  className={styles['features-title']}
                >
                  {text}
                </StyledTypographyUrbanistBody>
              </li>
            ))}
          </ul>

          <div className={styles.buttonsWrapper}>
            <Link target='_blank' href='https://cabinet.inqud.com/#/signup'>
              <ButtonGetStarted className={styles.getStarted}>
                {data.buttonScreen1GetStarted}
              </ButtonGetStarted>
            </Link>

            <HeroSectionModal data={data} />
          </div>

          <HeroSectionPaymentList params={params} />
        </div>

        <div className={styles.rightSide}>
          <Device device={TABLET_OR_DESKTOP}>
            <DynamicAnimatedFirstScreenVideo
              className={styles.graphic}
              height={595}
              timeRepeat={5000}
              urlFirstVideo='/video/video1.webm'
              urlSecondVideo='/video/video2.mp4'
              width={595}
            />
          </Device>
        </div>
      </div>
    </section>
  )
}
```

## Integration Localization (Datocms with next-intl)

## #Anchor links with sidebar