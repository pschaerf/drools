Patch/Fix-History




drools-persistance-jpa
	JpaPersistenceContextManager
		fixe beginCommandScopedEntityManager method in class org.drools.persistence.jpa.JpaPersistenceContextManager:
    /**
     * add: 
     *   if(cmdScopedEntityManager != null) {
     *   	this.cmdScopedEntityManager = cmdScopedEntityManager;
     *   }
     *   
     *   ...
     *   Caused by: java.lang.NullPointerException
     *		at org.jbpm.persistence.JpaProcessPersistenceContext.persist(JpaProcessPersistenceContext.java:22) [jbpm-persistence-jpa-5.3.0-SNAPSHOT.jar:]
     *		at org.jbpm.persistence.processinstance.JPAProcessInstanceManager.addProcessInstance(JPAProcessInstanceManager.java:49) [jbpm-persistence-jpa-5.3.0-SNAPSHOT.jar:]
     *		at org.jbpm.process.instance.AbstractProcessInstanceFactory.createProcessInstance(AbstractProcessInstanceFactory.java:36) [jbpm-flow-5.3.0-SNAPSHOT.jar:]
     *		at org.jbpm.process.instance.ProcessRuntimeImpl.startProcess(ProcessRuntimeImpl.java:182) [jbpm-flow-5.3.0-SNAPSHOT.jar:]
     *		at org.jbpm.process.instance.ProcessRuntimeImpl.createProcessInstance(ProcessRuntimeImpl.java:154) [jbpm-flow-5.3.0-SNAPSHOT.jar:]
     *		at org.jbpm.process.instance.ProcessRuntimeImpl.startProcess(ProcessRuntimeImpl.java:135) [jbpm-flow-5.3.0-SNAPSHOT.jar:]
     *		...
     */
    
    public void beginCommandScopedEntityManager() {
        EntityManager cmdScopedEntityManager = (EntityManager) env.get( EnvironmentName.CMD_SCOPED_ENTITY_MANAGER );
        
        if(cmdScopedEntityManager != null) {
        	this.cmdScopedEntityManager = cmdScopedEntityManager;
        }
        
        if ( cmdScopedEntityManager == null || 
           ( this.cmdScopedEntityManager != null && !this.cmdScopedEntityManager.isOpen() )) {
            internalCmdScopedEntityManager = true;
   
            this.cmdScopedEntityManager = this.emf.createEntityManager(); // no need to call joinTransaction as it will do so if one already exists
            this.cmdScopedEntityManager.setFlushMode(FlushModeType.COMMIT);
            this.env.set( EnvironmentName.CMD_SCOPED_ENTITY_MANAGER,
                          this.cmdScopedEntityManager );
            cmdScopedEntityManager = this.cmdScopedEntityManager;
        } else {
            internalCmdScopedEntityManager = false;
        }
        cmdScopedEntityManager.joinTransaction();
        appScopedEntityManager.joinTransaction();
    }	


