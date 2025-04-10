# A Sample Plugin

This repository contains a sample plugin for the Open edX project that aims to
provide examples for the various different plugin interfaces in the Open edX
platform.

The `frontend` folder makes use of the frontend-plugin-framework and slots to
add new components and functionality to multiple open edx frontends.

The `backend` folder will contain a python django app that can that can be
installed alongside edx-platform and makes use of the events and filters
capabilities to modify and work with existing functionality.

The `tutor` folder contains a tutor plugin that will install and setup both the
backend and frontend plugins in a deployment of tutor.

# Local Development for the Frontend Plugin w/Tutor

When developing with tutor, it's useful to use tutor to start up the rest of
the stack and then run the specific MFE you want to develop on your local
machine.  We just need to tell tutor to not start the specific MFE as a part of
the tutor-mfe plugin.

```
# Example using the `learner-dashboard` MFE
# Tell tutor to start this MFE in dev mode
tutor mounts add ~/src/openedx/frontend-app-learner-dashboard
tutor dev start learner-dashbard
# Update all the settings and start up everything
tutor dev launch
tutor dev stop
tutor dev start lms cms mfe
```

## Go to the `learner-dashboard` MFE folder locally.
```
npm install /path/to/sample-plugin/frontend
```

## Add an env.config.jsx
This file is not checked in and imports and injects your plugin for local
development.

```
import { DIRECT_PLUGIN, PLUGIN_OPERATIONS } from '@openedx/frontend-plugin-framework';
import { CourseList } from '@feanil/sample-plugin';

const config = {
  pluginSlots: {
    course_list_slot: {
      // Hide the default CourseList component
      keepDefault: false,
      plugins: [
        {
          op: PLUGIN_OPERATIONS.Insert,
          widget: {
            id: 'custom_course_list',
            type: DIRECT_PLUGIN,
            priority: 60,
            // The CourseList component recieves `courseListData` because that is what
            // the `custom_course_list` slot provides as a plugin prop.
            // https://github.com/openedx/frontend-app-learner-dashboard/tree/master/src/plugin-slots/CourseListSlot#plugin-props
            RenderWidget: CourseList
          },
        },
      ],
    },
  },
}

export default config;
```

## Add a module.config.js

This file tells webpack to use your local repo for the code of the module rather
than the `npm install` version of your package.

```
module.exports = {
  /*
  Modules you want to use from local source code.  Adding a module here means that when this app
  runs its build, it'll resolve the source from peer directories of this app.

  moduleName: the name you use to import code from the module.
  dir: The relative path to the module's source code.
  dist: The sub-directory of the source code where it puts its build artifact.  Often "dist".
  */

  localModules: [
    { moduleName: '@feanil/sample-plugin', dir: '/path/to/sample-plugin/frontend'},
  ],

};
```

# Start up the service
npm ci
npm start dev
```
