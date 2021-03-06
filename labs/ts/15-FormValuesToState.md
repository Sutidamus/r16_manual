# Lab 15: Form Values to State

## Objectives

- [ ] Add form data to component state
- [ ] Make form fields controlled components
- [ ] Handle submission of the form

## Steps

### Add form data to component state

1. **Open** the **file** `src\projects\ProjectForm.tsx`.
1. On the `ProjectFormProps` **interface** add the `project` prop to it.

   #### `src\projects\ProjectForm.tsx`

   ```diff
   interface ProjectFormProps {
   + project: Project;
   onSave: (project: Project) => void;
   onCancel: () => void;
   }
   ```

1. Create a `ProjectFormState` **interface** and add the `project` prop to it. Then add it as the second `Type` to the `React.Component<ProjectFormProps, ProjectFormState>`.

   #### `src\projects\ProjectForm.tsx`

   ```diff
   + interface ProjectFormState {
   +  project: Project;
   + }

   class ProjectForm extends React.Component<ProjectFormProps,
   + ProjectFormState
   >{
   ...
   }
   ```

2) **Add** a `project` object `state.project` to the `state` of the component and initialize the value to `props.project`.

   #### `src\projects\ProjectForm.tsx`

   ```diff
   class ProjectForm extends React.Component<ProjectFormProps, ProjectFormState> {
   + state = {
   +    project: this.props.project
   + };
      ...
   }
   ```

### Make form fields controlled components

1. **Make** all `<input />`s and `<textarea />`s **controlled** **components** by assigning their values to a `project` property on `state`.

   - **Write** a `handleChange` event handler and wire it up to `onChange` event of all the form fields.
   - The form field **types** that need to be handled include:

     - `<input type="text" />`
     - `<input type="number" />`
     - `<input type="checkbox" />`
     - `<textarea />`
       > Alternatively, you could write a separate handler for each of the form field types and invoke them as appropriate.

     #### `src\projects\ProjectForm.tsx`

     ```diff
     class ProjectForm extends React.Component<ProjectFormProps, ProjectFormState> {
       state = {
        project: this.props.project
       };

     +  handleChange = (event: any) => {
     +    const { type, name, value, checked } = event.target;
     +    let updatedValue = type === 'checkbox' ? checked : value;
     +    if (type === 'number') {
     +      updatedValue = +updatedValue;
     +    }
     +    const updatedProject = {
     +      [name]: updatedValue
     +    };
     +
     +    this.setState((previousState: ProjectFormState) => {
     +      // Shallow clone using Object.assign while updating changed property
     +      const project = Object.assign(
     +        new Project(),
     +        previousState.project,
     +        updatedProject
     +      );
     +      return { project };
     +    });
     +  };

      render() {
        const { onCancel } = this.props;
        return (
            <form
            className="input-group vertical"
            onSubmit={event => {
            this.handleSubmit(event);
            }}
            >
            <label htmlFor="name">Project Name</label>
            <input
            type="text"
            name="name"
            placeholder="enter name"
     +      value={this.state.project.name}
     +      onChange={this.handleChange}
            />
            <label htmlFor="description">Project Description</label>
            <textarea
            name="description"
            placeholder="enter description"
     +      value={this.state.project.description}
     +      onChange={this.handleChange}
            />
            <label htmlFor="budget">Project Budget</label>
            <input
            type="number"
            name="budget"
            placeholder="enter budget"
     +      value={this.state.project.budget}
     +      onChange={this.handleChange}
            />
            <label htmlFor="isActive">Active?</label>
            <input
            type="checkbox"
            name="isActive"
     +      checked={this.state.project.isActive}
     +      onChange={this.handleChange}
            />
            <div className="input-group">
            <button className="primary bordered medium">Save</button>
            <span />
            <button type="button" className="bordered medium" onClick={onCancel}>
                  cancel
            </button>
            </div>
            </form>
      );
      }

     }
     ```

### Handle submission of the form

1. In `handleSubmit`, when calling the `onSave` `prop` pass `state.project` instead of `new Project({ name: 'Updated Project' })`.

   #### `src\projects\ProjectForm.tsx`

   ```diff
   class ProjectForm extends React.Component<ProjectFormProps, ProjectFormState> {
   state = {
   project: this.props.project
   };
   ...

   handleSubmit = (event: SyntheticEvent) => {
   event.preventDefault();
   -    this.props.onSave(new Project({ name: 'Updated Project' }));
   +    this.props.onSave(this.state.project);
   };
   ```

2. In `ProjectList` **set** the `project` **prop** on the `<ProjectForm />` in the render method of the component.

   #### `src\projects\ProjecList.tsx`

   ```diff
   class ProjectList extends React.Component<ProjectListProps, ProjectListState> {
   ...
   render() {
      const { projects, onSave } = this.props;

      let item: JSX.Element;
      const items = projects.map((project: Project) => {
            if (project !== this.state.editingProject) {
            item = (
            ...
            );
            } else {
            item = (
            <div key={project.id} className="cols-sm">
                  <ProjectForm
   +              project={project}
                  onSave={onSave}
                  onCancel={this.cancelEditing}
                  ></ProjectForm>
            </div>
            );
            }
            return item;
      });

     return <div className="row">{items}</div>;
   }
   }
   ```

3. ProjectsPage update the project.

   #### `src\projects\ProjectsPage.tsx`

   ```diff
   import React, { Fragment } from 'react';
   import { MOCK_PROJECTS } from './MockProjects';
   import ProjectList from './ProjectList';
   + import { Project } from './Project';

   + interface ProjectsPageState {
   +  projects: Project[];
   + }

   class ProjectsPage extends React.Component<any, ProjectsPageState> {
   +  state = {
   +    projects: MOCK_PROJECTS
   +  };
     saveProject = (project: Project) => {
   -    console.log('Saving project: ', project);
   +    this.setState((previousState: ProjectsPageState) => {
   +      let projects = previousState.projects.map((p: Project) => {
   +        return p.id === project.id ? Object.assign(new Project(), p, project) : p;
   +      });
   +      return { projects };
   +    });
     };
     render() {
       return (
         <Fragment>
           <h1>Projects</h1>
   -       <ProjectList onSave={this.saveProject} projects={MOCK_PROJECTS} />
   +       <ProjectList onSave={this.saveProject} projects={this.state.projects} />
         </Fragment>
       );
     }
   }
   ```

4. **Verify** the application is working by following these **steps** in your browser.
   1. **Click** the **edit** button for a project.
   2. **Change** the **project name** in the form.
   3. **Click** **save** on the form.
   4. **Verify** the card shows the **updated** data.
      > Note that if you refresh your browser page your changes will not persist because the updates are only happening in the browser's memory. We will get a more permanent save working in a future lab when we communicate to our backend web API.

---

### &#10004; You have completed Lab 15
