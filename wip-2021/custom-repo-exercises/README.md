Integrated Management Workshop: Configuring a custom repository in Satellite

In this part of the workshop, we will learn how to configure a custom repository in Satellite. When you have RHEL clients managed by Satellite in your environment, sometimes there is a requirement to manage 3rd-party software packages that don't come from any of the RHEL distributions/repositories. For example, the Creative department wanted you to manage a 3rd-party CAD/CAM software that runs on many of their RHEL workstations.

Environment

-   Red Hat Satellite 6

-   2 x Red Hat Enterprise Linux clients

Pre-requisites (completed in previous exercises in the workshop)

-   Organization to be used = myorg

-   Location to be used = Default Location

-   TBA

Exercise

1\. Logging in to Satellite

-   Use a web browser on your computer to access the Satellite GUI via the link found in the Environment above. And use the following username and password to login: admin / ansible123

![](https://lh3.googleusercontent.com/E7feHyVF0hUr0ySyPm12NTdVZuLqSxVeRg30JZ63XorJSVAnOZDfGrW8h4f9xvStN9gp_Sx48ArGaThHSFuG9PcQsSdjqS7KDrpZ3OhkpqnLQ_RNsgTxVglJk90LNZfG2QLk6ULK)

-   Once you're in Satellite you would be able to see a dashboard

![](https://lh5.googleusercontent.com/g22VsrKilxepC3DSpw-tGWM9YOFHfrH2z8GRmwj6s_0369aX9JhXY0K2YGBI13mM7xB1CiWAOLg1CbQrNchNsWZsUgDBO3bNd9J_3bF5dQO0A1GztjMyVNx1OUrLM5STVk50Wx1a)

2\. Set Organization and Location (values to be changed)

-   Ensure that you select "myorg" as the Organization and "Default Location" as the location, by clicking on the menu on top of the page

![](https://lh5.googleusercontent.com/eQPZFalUi2kSLW2K7ejRnPnEl0pj_3msuo4QXKv8BKvKhAz-ClEA8FQspIhH8ukhfMHXc4VYJOYSYvgPZRnaKx94ZDaBqxDPYjeZqj-yGAKnwZBivyEksD7YIaurxnIxNCmAkWss)
