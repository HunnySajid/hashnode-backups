## How to Run Lambda on a Schedule 

 Running arbitrary code on a schedule is a very common use case. A common way of doing that is by using [cron jobs](https://en.wikipedia.org/wiki/Cron). But how would you do that in the cloud (specifically AWS) without having to run a Linux instance or something similar?

## What We Are Going to Build

One way of doing that is by leveraging **Amazon EventBridge and Lambda**. We will be creating a reusable AWS CDK construct that deploys a Lambda which will be executed on a configurable schedule. We will use that construct to deploy an example Lambda which fetches a quote from a 3rd-party API and logs it to the console. This is how it looks like:

![scheduled-lambda.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1634487709253/YwEjT3PVO.png)

You can find the [finished example on GitHub](https://github.com/JannikWempe/scheduled-lambda).

> 💡 I'll add some tips that are optional and just reflect the way I like doing stuff. They are formatted like this paragraph.

## Creating the Construct

We'll create the Lambda part of the construct first. We'll be getting to the EventBridge part after that. The construct will be called `ScheduledLambda`.

But first of all, let's get started by initializing the project using `aws-cdk` and creating the file for the construct by executing the following commands:

```bash
npx aws-cdk init app --language typescript
touch lib/scheduled-lambda.ts
```

>💡 I always use `npx aws-cdk` instead of installing the CLI globally. That way I always use the most recent version of the CLI. I don't mind the little extra time it takes because I don't initialize projects that often.

### Adding the Lambda

I default to use `NodejsFunction` instead of `Lambda` because it comes with some handy additional features. It uses `esbuild` to transpile and bundle the Lambda code. That way you can use TypeScript and install dependencies using a dedicated `package.json` without worrying about anything. I love it! We'll use `node-fetch` later on in the example in order to make use of it.

> 💡 I install `esbuild` as a dev dependency. Without having that installed, [a Docker container containing esbuild will be used](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-lambda-nodejs-readme.html#local-bundling) which takes more time and space.

You can read more about `NodejsFunction` [in the docs](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-lambda-nodejs-readme.html).

You can install the required dependencies by executing:

```bash
npm install @aws-cdk/aws-lambda @aws-cdk/aws-lambda-nodejs
npm i -D @esbuild
```

Let me show you the code for the construct with just the Lambda part. I'll go through it step by step after that.

```typescript
// lib/scheduled-lambda.ts
import * as cdk from '@aws-cdk/core';
import {Construct} from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';
import {NodejsFunction, NodejsFunctionProps} from "@aws-cdk/aws-lambda-nodejs";
import {LogGroup, LogGroupProps} from '@aws-cdk/aws-logs';

interface LambdaProps extends NodejsFunctionProps {
  // name is determined by the lambda created, it should not be changed
  logGroupProps?: Omit<LogGroupProps, "logGroupName">;
}

interface ScheduledLambdaProps {
  lambdaProps?: LambdaProps;
}

const defaultLambdaProps: Partial<NodejsFunctionProps> = {
  handler: 'handler',
  memorySize: 128,
  runtime: lambda.Runtime.NODEJS_14_X,
}

export class ScheduledLambda extends cdk.Construct {
  lambda: NodejsFunction;

  constructor(scope: Construct, id: string, props?: ScheduledLambdaProps) {
    super(scope, id);

    this.lambda = this.createLambda(props?.lambdaProps);
  }

  private createLambda(lambdaProps?: ScheduledLambdaProps["lambdaProps"]) {
    const lambda = new NodejsFunction(this, "Lambda", {
      ...defaultLambdaProps,
      ...lambdaProps
    });
    new LogGroup(this, 'LogGroup', {
      // this name makes it replace the default log group
      logGroupName: '/aws/lambda/' + lambda.functionName,
      ...lambdaProps?.logGroupProps
    });
    return lambda;
  }
}
```

As you can see it is defining the `ScheduledLambda` construct by extending from `cdk.Construct`. The `constructor` takes some custom props, which are optional because `NodejsFunction` doesn't require any props due to reasonable defaults.

> 💡 I prefer to define some defaults just to make them obvious and keep them stable just in case any default will change in the future. In this case, it does not make any difference because my defaults are exactly the same being used by `NodejsFunction`.

> 💡 I always create a `LogGroup` alongside any Lambda. That is why I added the optional `logGroupProps` to the `LambdaProps`. Why am I doing that? As a default, there will be a Log Group automatically created for you. But the logs will never expire and the Log Group will not be deleted when destroying the stack. I get more control over the Log Group by creating it myself. The Log Group is associated with the Lambda by using the name of the Lambda prefixed with `'/aws/lambda/'` as the `logGroupName`.

The Lambda and the associated Log Group are created by calling the `createLambda` method and passing `lambdaProps`. Also, we assign the Lambda to the public property `lambda` which makes the Lambda accessible outside of the construct.

### Add the Scheduling Functionality

As already mentioned, we'll be using an EventBridge Rule that is running on a schedule. First of all, we have to install the required dependencies:

```bash
npm i @aws-cdk/aws-events @aws-cdk/aws-events-targets
```

There are three different ways of creating a schedule: using an expression, a rate or cron ([learn more](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule-schedule.html)). Here are the three static factory methods for creating a schedule from the `@aws-cdk/aws-events` source code:

```typescript
// @aws-cdk/aws-events/lib/schedule.ts
export declare abstract class Schedule {
    /**
     * Construct a schedule from a literal schedule expression.
     *
     * @param expression The expression to use.
     * @stability stable
     */
    static expression(expression: string): Schedule;
    /**
     * Construct a schedule from an interval and a time unit.
     *
     * @stability stable
     */
    static rate(duration: Duration): Schedule;
    /**
     * Create a schedule from a set of cron fields.
     *
     * @stability stable
     */
    static cron(options: CronOptions): Schedule;
    /**
     * Retrieve the expression for this schedule.
     *
     * @stability stable
     */
    abstract readonly expressionString: string;
    /**
     * @stability stable
     */
    protected constructor();
}
```

We'll let the user of the construct pass an instance of `Schedule`. That way it is up to the user to decide about how to describe the schedule. Here is the finished construct:

```typescript
import * as cdk from '@aws-cdk/core';
import {Construct} from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';
import {NodejsFunction, NodejsFunctionProps} from "@aws-cdk/aws-lambda-nodejs";
import * as events from "@aws-cdk/aws-events";
import * as targets from "@aws-cdk/aws-events-targets";
import {LogGroup, LogGroupProps} from '@aws-cdk/aws-logs';

interface LambdaProps extends NodejsFunctionProps {
  // name is determined by the lambda created
  logGroupProps?: Omit<LogGroupProps, "logGroupName">;
}

// no targets, because the lambda is the only target
interface RuleProps extends Omit<events.RuleProps, "targets"> {
  // schedule is always required instead of optional
  schedule: events.Schedule;
}

interface ScheduledLambdaProps {
  lambdaProps?: LambdaProps;
  ruleProps: RuleProps;
}

const defaultLambdaProps: Partial<NodejsFunctionProps> = {
  handler: 'handler',
  memorySize: 128,
  runtime: lambda.Runtime.NODEJS_14_X,
}

export class ScheduledLambda extends cdk.Construct {
  lambda: NodejsFunction;

  constructor(scope: Construct, id: string, props: ScheduledLambdaProps) {
    super(scope, id);

    this.lambda = this.createLambda(props.lambdaProps);
    this.scheduleLambda(props.ruleProps);
  }

  private createLambda(lambdaProps?: ScheduledLambdaProps["lambdaProps"]) {
    const lambda = new NodejsFunction(this, "Lambda", {
      ...defaultLambdaProps,
      ...lambdaProps
    });
    new LogGroup(this, 'LogGroup', {
      // this name makes it replace the default log group
      logGroupName: '/aws/lambda/' + lambda.functionName,
      ...lambdaProps?.logGroupProps
    });
    return lambda;
  }

  private scheduleLambda(ruleProps: ScheduledLambdaProps["ruleProps"]) {
    const rule = new events.Rule(this, 'Schedule', ruleProps);
    rule.addTarget(new targets.LambdaFunction(this.lambda));
  }
}
```

We narrow down the type of the `RuleProps`. We want to make it mandatory to pass a `schedule` and not allow the user to define `targets` because the Lambda should be the only target.

In the `scheduleLambda` method, we instantiate the rule and assign the previously created Lambda as a target to it. That's it. The construct is ready for being used. That is what we'll be doing next in the example.

## Using the Construct

Okay, let's use the construct. As mentioned in the beginning, we'll be creating a Lambda fetching a random quote from an API. First of all, we'll create the files for the Lambda and install `node-fetch`:

```batch
mkdir -p lambdas/random-quote
cd lambdas/random-quote
npm init -y
npm install node-fetch
touch index.ts
```

Alright, next up create the Lambda:

```typescript
// lambdas/random-quote/index.ts
import fetch from "node-fetch";

type Quote = {
  _id: string;
  tags: string[];
  content: string;
  author: string;
  authorSlug: string;
  length: number;
  dateAdded: string;
  dateModified: string;
}

export const handler = async () => {
  const randomQuoteResponse = await fetch("https://api.quotable.io/random");
  const quote = await randomQuoteResponse.json() as Quote; // using unknown + type guard would be more bullet proof
  console.log(quote.content);
}
```

Nothing special here. We don't need any information about the `event` or `context`. Therefore we don't pass any arguments to the `handler`.

This is how you use the construct in order to create the underlying resources:

```typescript
// lib/scheduled-lambda-stack.ts
import * as path from "path";

import * as cdk from '@aws-cdk/core';
import {Duration, RemovalPolicy} from '@aws-cdk/core';
import {ScheduledLambda} from "./scheduled-lambda";
import * as events from "@aws-cdk/aws-events";
import {RetentionDays} from "@aws-cdk/aws-logs";

export class ScheduledLambdaStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    new ScheduledLambda(this, "DailyRandomQuote", {
      lambdaProps: {
        entry: path.join(__dirname, "..", "lambdas", "random-quote", "index.ts"),
        logGroupProps: {
          removalPolicy: RemovalPolicy.DESTROY,
          retention: RetentionDays.ONE_WEEK,
        }
      },
      ruleProps: {
        schedule: events.Schedule.rate(Duration.minutes(5))
      }
    });
  }
}
```

Make sure to pass the correct `entry` prop pointing to the Lambda. If you want to name your handler function used in the Lambda differently, you must also pass the `handler` alongside the `entry`.

> 💡 Note how I pass the `removalPolicy` and the `retention` to the `ScheduledLambda` `constructor`. That way we don't clutter our AWS account as previously mentioned in the tip about the Log Group.

In this example, we invoke the Lambda every 5min by using the `Schedule.rate` factory function. That is it. Simple API, right? Let's build and deploy:

```bash
# in the project root
npm run build
npx cdk deploy
```

> ℹ️ `npx cdk deploy` will deploy the stack to the account you are currently logged in. You can [learn here about how to log in](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) using the AWS CLI.

That is it. The example will be deployed and you can see the random quotes in the AWS Console under CloudWatch -> Log groups.

> ⚠️ Make sure to destroy the stack afterwards using `npx cdk destroy` in order to remove the resources. Otherwise, the Lambda will be executed every 5min.

## Conclusion
It is relatively straightforward to create a reusable construct in order to schedule the execution of a Lambda. You could also publish the construct and make it available for other or other projects of yourself. (Probably there is something similar available somewhere?).

I personally use exact this pattern to buy some cryptos every week. The use cases for executing code on a schedule are almost endless.